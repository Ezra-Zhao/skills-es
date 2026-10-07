---
name: generador-de-pruebas
description: >-
  Úsalo cuando el usuario pida pruebas, tests o cobertura para una función o
  módulo. Lee el código real, identifica casos normales, casos borde y errores,
  y genera un archivo de pruebas pytest listo para ejecutar con el patrón
  Arrange-Act-Assert. Verifica que todas las pruebas pasen antes de entregar.
license: MIT
metadata:
  author: "Ezra-Zhao"
  version: "1.0.0"
---

# Generador de pruebas

Escribes pruebas automatizadas para código Python existente. Lees la función real, no inventas su comportamiento: cada prueba verifica algo que el código hace de verdad.

## Cuándo usar

- El usuario dice "escribe pruebas", "genera tests" o "añade cobertura" para una función o módulo.

## Procedimiento

### 1. Lee el código bajo prueba

Abre el archivo y anota:

- Qué recibe la función (tipos, valores por defecto).
- Qué devuelve en el caso normal.
- Qué casos borde existen (vacío, cero, `None`, listas vacías, valores negativos).
- Qué errores lanza y cuándo (`ValueError`, `KeyError`…).

### 2. Diseña los casos

Cubre como mínimo:

- El camino feliz (happy path).
- Cada caso borde relevante.
- Cada excepción documentada, verificada con `pytest.raises`.

### 3. Escribe el archivo de pruebas

- Nombre: `tests/test_<modulo>.py` (el nombre del módulo con prefijo `test_`).
- Una función de prueba por caso, con nombre descriptivo: `test_dividir_con_negativos`.
- Patrón Arrange-Act-Assert: prepara los datos, ejecuta, verifica.
- Agrupa casos similares con `@pytest.mark.parametrize`.

Ejemplo de estructura:

```python
import pytest
from paquete.modulo import dividir


def test_dividir_caso_normal():
    # Arrange
    a, b = 10, 2
    # Act
    resultado = dividir(a, b)
    # Assert
    assert resultado == 5


@pytest.mark.parametrize("a,b,esperado", [(0, 5, 0), (-10, 2, -5), (7, 2, 3.5)])
def test_dividir_varios_casos(a, b, esperado):
    assert dividir(a, b) == esperado


def test_dividir_por_cero_lanza_error():
    with pytest.raises(ValueError):
        dividir(10, 0)
```

### 4. Ejecuta y verifica

```bash
pip install pytest pytest-cov   # solo si faltan
pytest tests/test_modulo.py -q
```

Todas las pruebas deben pasar antes de entregar. Si alguna falla, corrige la prueba… o señala el bug real si el problema está en el código.

Para medir la cobertura:

```bash
pytest --cov=paquete --cov-report=term-missing
```

Apunta a cubrir las ramas nuevas e informa el porcentaje alcanzado.

## Reglas

- No modifiques el código bajo prueba para que las pruebas pasen, salvo que encuentres un bug real: en ese caso repórtalo primero.
- Alternativa sin pytest, con la biblioteca estándar:

```bash
python -m unittest discover -s tests -v
```
