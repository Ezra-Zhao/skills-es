---
name: optimizador-sql
description: >-
  Úsalo cuando el usuario pida optimizar una consulta SQL, diga que una
  consulta "va lenta" o quiera revisar índices. Analiza la consulta con
  EXPLAIN QUERY PLAN en sqlite3, detecta antipatrones (SELECT *, LIKE con
  comodín inicial, índices faltantes) y propone la reescritura y los índices
  concretos. Mide el tiempo antes y después cuando sea posible.
license: MIT
metadata:
  author: "Ezra-Zhao"
  version: "1.0.0"
---

# Optimizador SQL

Ayudas a que las consultas SQL vayan más rápido. Diagnosticas con el plan de ejecución real, no con intuición, y propones cambios medibles: reescritura de la consulta e índices concretos.

## Cuándo usar

- El usuario dice "optimiza esta consulta", "esta consulta va lenta" o "revisa los índices".

## Procedimiento

### 1. Mide el punto de partida

```bash
sqlite3 basedatos.db
```

Dentro del cliente:

```sql
.timer on
SELECT ...;   -- la consulta lenta, tal cual está hoy
```

`.timer on` muestra el tiempo real de cada consulta. Anota el tiempo antes de tocar nada.

### 2. Lee el plan de ejecución

```sql
EXPLAIN QUERY PLAN SELECT ...;
```

Busca estas señales:

- **SCAN** sobre una tabla grande sin usar índice → probablemente falta un índice.
- **SEARCH** usando un índice → va por buen camino.
- **USE TEMP B-TREE FOR ORDER BY / GROUP BY** → ordena o agrupa sin un índice útil.

### 3. Revisa los índices existentes

```sql
SELECT name, tbl_name, sql FROM sqlite_master WHERE type = 'index';
```

### 4. Detecta antipatrones comunes

- `SELECT *` cuando solo se necesitan algunas columnas.
- `WHERE columna LIKE '%texto'` — el comodín inicial impide usar el índice.
- Funciones sobre columnas indexadas: `WHERE YEAR(fecha) = 2024` no usa el índice de `fecha`; mejor un rango de fechas.
- `OR` sobre columnas distintas; a veces dos consultas unidas con `UNION` rinden mejor.
- `JOIN` sin índice en la columna de unión.
- Consultas N+1 desde la aplicación (una consulta por cada fila de otra).
- Subconsultas correlacionadas que pueden reescribirse como `JOIN`.

### 5. Propón la mejora

1. **Reescritura** de la consulta cuando el problema es la forma (columnas explícitas, rangos en vez de funciones, `UNION` en vez de `OR`).
2. **Índices concretos**, con la sentencia lista para ejecutar:

```sql
CREATE INDEX idx_pedidos_cliente_fecha ON pedidos(cliente_id, fecha);
```

Ordena las columnas del índice: primero las de igualdad (`=`), luego las de rango u orden.

3. Tras crear el índice, actualiza las estadísticas del optimizador:

```sql
ANALYZE;
```

### 6. Verifica

```sql
.timer on
SELECT ...;   -- la consulta optimizada
```

Compara los tiempos y reporta la mejora. Si no hay mejora, dilo: no todos los problemas se resuelven con índices.

## Reglas

- Un índice acelera lecturas pero ralentiza escrituras: no añadas índices a todo, propón solo los que el plan justifica.
- Comprueba que la consulta reescrita devuelva los mismos resultados (mismo número de filas en una muestra).
