# skills-es 🇪🇸

Paquete de **Agent Skills en español**: carpetas listas para instalar con archivos `SKILL.md` que tu agente de programación descubre y carga automáticamente. Cada skill incluye instrucciones en español, el procedimiento paso a paso y comandos reales verificados.

Compatible con Claude Code, Codex, Cursor, GitHub Copilot / VS Code, Antigravity y cualquier agente compatible con el formato [Agent Skills](https://agentskills.io).

## Skills incluidas

| Skill | Qué hace |
|---|---|
| [`revisor-de-codigo`](revisor-de-codigo/) | Revisa cambios con `git diff`, ejecuta análisis estático con `ruff` y entrega hallazgos clasificados por severidad con archivo, línea y sugerencia de corrección |
| [`mensaje-de-commit`](mensaje-de-commit/) | Analiza `git diff --cached` y propone mensajes de commit en español según Conventional Commits (imperativo, máx. 72 caracteres) |
| [`depurador`](depurador/) | Lee tracebacks de abajo hacia arriba, reproduce el fallo, aísla la causa raíz (con `git bisect` y `pdb` si hace falta) y propone la corrección mínima |
| [`generador-de-pruebas`](generador-de-pruebas/) | Lee una función real y genera pruebas `pytest` con el patrón Arrange-Act-Assert, casos borde y medición de cobertura |
| [`documentador-api`](documentador-api/) | Inspecciona las rutas reales de una API REST y genera un `openapi.yaml` válido (OpenAPI 3.1), con validación de sintaxis |
| [`auditoria-seguridad`](auditoria-seguridad/) | Escanea código Python con `bandit` y dependencias con `pip-audit`, más revisión manual guiada por el OWASP Top 10:2025 |
| [`optimizador-sql`](optimizador-sql/) | Diagnostica consultas lentas con `EXPLAIN QUERY PLAN` en `sqlite3`, detecta antipatrones y propone reescritura e índices concretos |

## Instalación

Copia la carpeta de la skill que quieras al directorio de skills de tu agente:

| Agente | Directorio de skills |
|---|---|
| Claude Code | `~/.claude/skills/` |
| Codex | `~/.codex/skills/` |
| Cursor | `~/.cursor/skills/` |
| GitHub Copilot / VS Code | `~/.copilot/skills/` |
| Antigravity CLI | `.agents/skills/` en tu proyecto |

Ejemplo:

```bash
git clone https://github.com/Ezra-Zhao/skills-es.git
cp -r skills-es/revisor-de-codigo ~/.claude/skills/
```

Instalación para todo el equipo: coloca la carpeta en `.agents/skills/` dentro del repositorio (la mayoría de los agentes de 2026 lo leen a nivel de proyecto; Claude Code usa `.claude/skills/`).

## Uso

Una vez instalada, el agente detecta la skill automáticamente cuando tu petición coincide con su descripción. También puedes invocarla de forma explícita, por ejemplo:

- "Revisa mi código con revisor-de-codigo"
- "Genera el mensaje de commit"
- "Audita la seguridad del proyecto"

Las herramientas externas que usa cada skill (`ruff`, `bandit`, `pip-audit`, `pytest`) se instalan con `pip` solo si no están presentes; cada `SKILL.md` indica el comando exacto.

## Seguridad

Las skills se ejecutan con los permisos de tu agente: tu terminal, tus archivos, tus credenciales. Trátalas como software, no como documentos: lee el `SKILL.md` antes de instalar. Ninguna skill de este paquete hace llamadas de red salvo las propias herramientas de escaneo (`pip-audit` consulta la base de datos de vulnerabilidades) y nada se ejecuta en el momento de la instalación.

## Autor

**Ezra-Zhao** — https://github.com/Ezra-Zhao

## Licencia

MIT — ver [LICENSE](LICENSE).

---

## English summary

**skills-es** is a drop-in package of Agent Skills written natively in Spanish: 7 installable skill folders (`SKILL.md` each) covering code review, Conventional Commits message generation (in Spanish), debugging, pytest test generation, OpenAPI 3.1 documentation, OWASP Top 10:2025 security auditing (`bandit` + `pip-audit`), and SQL query optimization (`sqlite3` + `EXPLAIN QUERY PLAN`). Works with Claude Code, Codex, Cursor, Copilot, Antigravity, and any Agent Skills-compatible agent. Author: Ezra-Zhao. License: MIT.
