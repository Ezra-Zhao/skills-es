---
name: auditoria-seguridad
description: >-
  Úsalo cuando el usuario pida una auditoría de seguridad, diga "revisa la
  seguridad" o mencione OWASP. Ejecuta bandit sobre el código Python y
  pip-audit sobre las dependencias, y completa con una revisión manual guiada
  por el OWASP Top 10:2025. Entrega hallazgos con severidad, ubicación y
  remediación concreta. Es una revisión de base; no sustituye una auditoría
  profesional.
license: MIT
metadata:
  author: "Ezra-Zhao"
  version: "1.0.0"
---

# Auditoría de seguridad

Realizas una auditoría de seguridad básica de un proyecto Python: escaneo automático del código, escaneo de dependencias y revisión manual guiada por el OWASP Top 10:2025. Al final entregas un informe priorizado.

## Cuándo usar

- El usuario dice "audita la seguridad", "revisa la seguridad del proyecto" o menciona OWASP.

## Procedimiento

### 1. Escaneo estático del código

```bash
pip install bandit        # solo si falta
bandit -r src/
```

Para guardar un informe reutilizable:

```bash
bandit -r src/ -f json -o informe-bandit.json
```

Revisa cada aviso de severidad media o alta. Los de severidad baja anótalos solo si son relevantes para el proyecto.

### 2. Escaneo de dependencias

```bash
pip install pip-audit     # solo si falta
pip-audit
pip-audit -r requirements.txt   # si el proyecto usa requirements
```

Por cada vulnerabilidad encontrada anota: paquete, versión instalada, identificador del aviso y versión que la corrige.

### 3. Búsqueda de patrones peligrosos

```bash
grep -rn --include="*.py" -E "\beval\(|\bexec\(" src/
grep -rn --include="*.py" "shell=True" src/
grep -rn --include="*.py" -E "pickle\.loads|yaml\.load\(" src/
git ls-files | grep -E "(\.env$|id_rsa|secrets\.json)"   # secretos dentro del repo
```

Cada coincidencia se verifica a mano: el `grep` solo señala candidatos, no confirma vulnerabilidades.

### 4. Revisión manual con el OWASP Top 10:2025

Verifica estas categorías contra el código (edición 2025, vigente; reemplaza a la de 2021):

1. **A01 Control de acceso roto** — ¿se verifica la autorización en cada endpoint, no solo la autenticación? (SSRF ahora se evalúa aquí)
2. **A02 Configuración de seguridad** — valores por defecto inseguros, cabeceras ausentes, mensajes de error que filtran información interna.
3. **A03 Fallos en la cadena de suministro** — dependencias sin versión fijada o sin auditar.
4. **A04 Fallos criptográficos** — contraseñas en texto plano, hashes débiles (MD5/SHA1 para contraseñas), TLS sin verificar.
5. **A05 Inyección** — SQL, comandos u otros lenguajes construidos con datos del usuario sin parametrizar.
6. **A06 Diseño inseguro** — falta de límites de uso, flujos sin validación de reglas de negocio.
7. **A07 Fallos de autenticación** — política de contraseñas, bloqueo por intentos, gestión de sesiones.
8. **A08 Fallos de integridad de software o datos** — actualizaciones sin firma verificada, deserialización insegura.
9. **A09 Fallos de registro y alerta** — ¿quedan registrados los intentos fallidos de acceso?
10. **A10 Manejo inadecuado de condiciones excepcionales** — errores que dejan el sistema en un estado inconsistente.

### 5. Informe

Una tabla por hallazgo:

| Severidad | Ubicación | Hallazgo | Remediación |
|---|---|---|---|
| Alta | `app/auth.py:88` | Contraseña comparada en texto plano | Usar bcrypt/argon2 con salt |

Severidades: **Crítica** (explotable hoy), **Alta**, **Media**, **Baja**, **Informativa**.

Cierra con el total de hallazgos por severidad y los 3 más urgentes.

## Límites

- Es una revisión de base con herramientas automáticas. No detecta fallos de lógica de negocio ni sustituye una prueba de penetración profesional.
