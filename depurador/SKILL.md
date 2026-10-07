---
name: depurador
description: >-
  Úsalo cuando el usuario pegue un traceback, un mensaje de error o diga que
  algo "no funciona", "falla" o "se rompe". Lee el error de abajo hacia arriba,
  reproduce el fallo, aísla la causa raíz con evidencia y propone la corrección
  mínima más una prueba que evite la regresión. No para revisiones generales
  de código.
license: MIT
metadata:
  author: "Ezra-Zhao"
  version: "1.0.0"
---

# Depurador

Ayudas a encontrar la causa raíz de un error. Trabajas con evidencia, no con suposiciones: cada hipótesis se comprueba antes de proponer un cambio.

## Cuándo usar

- El usuario pega un traceback o un mensaje de error.
- Algo "no funciona", "falla" o "se rompe" en ejecución.

## Procedimiento

### 1. Lee el traceback de abajo hacia arriba

La última línea dice **qué** falló (el tipo de excepción y su mensaje). Las líneas superiores dicen **dónde**: archivo, línea y función de cada llamada, de la más externa a la más interna. La causa suele estar en los últimos 2–3 marcos que pertenecen al código del usuario, no a las librerías.

Preguntas guía:

- ¿Qué tipo de excepción es? (`TypeError`, `KeyError`, `ValueError`…)
- ¿Qué valor concreto la disparó? El mensaje suele decirlo.
- ¿En qué línea del código propio empezó el problema?

### 2. Reproduce el fallo

```bash
python -m pytest tests/test_relacionado.py -x --tb=short
```

- `-x` detiene en el primer fallo; `--tb=short` muestra el traceback compacto.
- Si no hay pruebas, reproduce con el script mínimo que dispare el error. Sin reproducción no hay diagnóstico fiable.

### 3. Aísla la causa

- Reduce el caso: quita código hasta quedarte con lo mínimo que sigue fallando.
- Si el error "apareció de repente", busca el commit culpable:

```bash
git stash                 # guarda los cambios sin commitear
git stash pop             # recupéralos al terminar
git bisect start
git bisect bad            # el estado actual falla
git bisect good <commit-que-funcionaba>
# git prueba cada commit intermedio; marca good/bad hasta encontrarlo
git bisect reset
```

- Para inspeccionar valores en vivo:

```bash
python -m pdb script.py
```

Comandos útiles dentro de pdb: `n` (siguiente línea), `c` (continuar), `p variable` (mostrar un valor), `l` (ver el código alrededor), `q` (salir).

### 4. Propón la corrección mínima

- Corrige la causa, no el síntoma: un `try/except` que oculte el error no es una corrección.
- Explica en una frase por qué ese cambio elimina la causa raíz.
- Añade o sugiere una prueba que reproduzca el caso y evite que vuelva a ocurrir.

## Formato de respuesta

1. **Diagnóstico**: qué falló y por qué, con archivo y línea.
2. **Evidencia**: qué comprobaste para confirmarlo.
3. **Corrección**: el cambio mínimo propuesto.
4. **Prevención**: prueba o verificación sugerida.
