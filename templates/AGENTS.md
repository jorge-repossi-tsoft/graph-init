# AGENTS.md
### Punto de entrada único — cualquier asistente (Claude Code, Antigravity, Cursor, Copilot) lee este archivo primero.

Este proyecto sigue el patrón **GRAPH**. Antes de actuar:

1. Leé `graph/GRAPH.md` — la spec completa del patrón.
2. Consultá `graph/knowledge/` antes de proponer cualquier cambio (principio G). Si está vacío, es un proyecto greenfield recién iniciado — está bien, pero el mecanismo de consulta igual debe usarse desde la primera tarea.
3. Revisá `graph/sessions/progress.md` y `tasks.md` para saber qué pasó antes de esta sesión (principio P).
4. Identificá tu rol en `roles/registry.yml` — cada rol tiene permisos distintos sobre `graph/`.
5. Cualquier propuesta de cambio con severidad `medium` o superior (ver `graph/gates/policy.yml`) va a `graph/gates/pending/` — no se ejecuta directo.
6. Estás sujeto a `graph/circuit-breaker.yml` en todo momento. No podés desactivarlo desde tu propio contexto.

## Bridges específicos por herramienta
- `CLAUDE.md` — bridge para Claude Code, apunta acá.
- (agregar bridges equivalentes para otras herramientas según se sumen)

## Regla de oro
Si una acción no está clasificada en `graph/gates/policy.yml`, tratala como severidad `high` por defecto. Nunca asumas bajo riesgo por omisión.
