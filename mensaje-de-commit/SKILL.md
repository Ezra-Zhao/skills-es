---
name: mensaje-de-commit
description: >-
  Úsalo cuando el usuario pida un mensaje de commit, diga "genera el commit" o
  quiera describir sus cambios. Analiza git status y git diff del área de
  staged, clasifica el cambio según Conventional Commits y propone el mensaje
  en español, en imperativo y con un título de máximo 72 caracteres. Nunca
  ejecuta git commit sin autorización explícita del usuario.
license: MIT
metadata:
  author: "Ezra-Zhao"
  version: "1.0.0"
---

# Generador de mensajes de commit

Generas mensajes de commit en español siguiendo la convención Conventional Commits. Analizas los cambios reales del repositorio; nunca inventas el contenido del mensaje.

## Cuándo usar

- El usuario dice "genera el mensaje de commit", "¿qué commit hago?" o "descríbeme los cambios".

## Procedimiento

### 1. Lee los cambios

```bash
git status --short
git diff --cached --stat
git diff --cached          # contenido real de lo que irá al commit
```

Si no hay nada en staged, avísalo y pregunta si quiere incluir los cambios pendientes (`git diff --stat`). No ejecutes `git add` por tu cuenta.

Contexto opcional para imitar el tono del proyecto:

```bash
git log --oneline -5
```

### 2. Clasifica el cambio

| Tipo | Cuándo usarlo |
|---|---|
| `feat` | Nueva funcionalidad |
| `fix` | Corrección de un bug |
| `docs` | Solo documentación |
| `style` | Formato o espacios, sin cambio de lógica |
| `refactor` | Reestructuración sin cambiar el comportamiento |
| `perf` | Mejora de rendimiento |
| `test` | Añade o corrige pruebas |
| `build` | Sistema de construcción o dependencias |
| `ci` | Configuración de integración continua |
| `chore` | Tareas menores que no encajan en lo anterior |
| `revert` | Revierte un commit anterior |

### 3. Redacta el mensaje

Formato:

```
tipo(ámbito): descripción breve en imperativo
```

Reglas:

- Título de máximo 72 caracteres, sin punto final.
- Imperativo en español, como dando una orden: "añade", "corrige", "elimina", "actualiza".
- Minúscula inicial en la descripción.
- El ámbito es opcional e indica el módulo o área: `feat(auth):`, `fix(api):`.

Ejemplos:

```
feat(auth): añade inicio de sesión con correo y contraseña
fix(api): corrige error 500 al crear usuario sin email
docs: actualiza la guía de instalación con los requisitos de Python
refactor(db): extrae la conexión a un módulo propio
```

Si el cambio lo justifica, añade un cuerpo con viñetas (qué y por qué, no cómo) y un pie:

```
feat(pagos): añade reintento automático de cargos fallidos

- Reintenta hasta 3 veces con espera exponencial
- Registra cada intento para auditoría

BREAKING CHANGE: el campo `intentos` ahora es obligatorio
```

### 4. Entrega

Muestra el mensaje propuesto en un bloque de código y espera la confirmación. **No ejecutes `git commit` salvo que el usuario lo pida explícitamente.**
