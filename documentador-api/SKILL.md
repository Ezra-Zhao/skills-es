---
name: documentador-api
description: >-
  Úsalo cuando el usuario pida documentar una API REST o generar su
  especificación OpenAPI. Inspecciona las rutas y manejadores reales del código
  y genera un archivo openapi.yaml válido (OpenAPI 3.1) con operaciones,
  parámetros, cuerpos de petición y esquemas de respuesta. Valida que el YAML
  sea sintácticamente correcto antes de entregar.
license: MIT
metadata:
  author: "Ezra-Zhao"
  version: "1.0.0"
---

# Documentador de API

Generas documentación de APIs REST en formato OpenAPI 3.1. Te basas en el código real (rutas, parámetros, respuestas); lo que no puedas inferir del código lo marcas como `TODO`, nunca lo inventas.

## Cuándo usar

- El usuario dice "documenta la API", "genera el OpenAPI" o "necesito la especificación".

## Procedimiento

### 1. Inspecciona el código

Busca las definiciones de rutas del framework en uso (decoradores `@app.get`, `@router.post`, `add_url_rule`, etc.) y para cada endpoint anota:

- Método HTTP y ruta (p. ej. `GET /usuarios/{id}`).
- Parámetros: en la ruta, en query o en cabeceras; cuáles son obligatorios.
- Cuerpo de la petición: campos esperados y sus tipos.
- Respuestas: códigos de estado que devuelve y forma del cuerpo.
- Autenticación requerida, si la hay.

### 2. Genera `openapi.yaml`

Estructura mínima válida (OpenAPI 3.1.0):

```yaml
openapi: 3.1.0
info:
  title: Nombre de la API
  version: 1.0.0
  description: Qué hace esta API
servers:
  - url: https://api.ejemplo.com/v1
paths:
  /usuarios/{id}:
    get:
      summary: Obtiene un usuario por su id
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: integer
      responses:
        '200':
          description: Usuario encontrado
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Usuario'
        '404':
          description: Usuario no encontrado
components:
  schemas:
    Usuario:
      type: object
      required: [id, nombre, email]
      properties:
        id:
          type: integer
        nombre:
          type: string
        email:
          type: string
          format: email
```

Reglas:

- Un `path` por endpoint, una operación por método HTTP.
- Todo parámetro de ruta lleva `required: true`.
- Reutiliza esquemas con `$ref` bajo `components/schemas` en lugar de repetirlos.
- Documenta los códigos de error que el código devuelve de verdad (400, 401, 404, 422, 500…).

### 3. Valida el archivo

```bash
pip install pyyaml   # solo si falta
python -c "import yaml; yaml.safe_load(open('openapi.yaml')); print('YAML válido')"
```

Si hay errores de sintaxis, corrígelos antes de entregar.

## Reglas

- No documentes endpoints que no existan en el código.
- Si el proyecto ya tiene un `openapi.yaml`, actualízalo en lugar de crear otro.
