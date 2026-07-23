# Tasks

> Estilo SDD (spec-driven development). Vive junto a `progress.md` pero con
> foco distinto: progress.md es "qué pasó", tasks.md es "qué falta y en qué orden".

## Instalación del patrón (marcar al hacer graph-init)
- [ ] Modo detectado: greenfield | brownfield
- [ ] Árbol de carpetas creado
- [ ] (brownfield only) Indexación inicial completa — bloqueante, ningún agente opera antes de esto
- [ ] (brownfield only) Reconciliación de historial vía git log completada, commits marcados `origen: pre-graph`
- [ ] circuit-breaker.yml revisado y ajustado a este proyecto (los defaults son conservadores)
- [ ] policy.yml revisado — ¿las severidades por defecto tienen sentido para este proyecto?
- [ ] roles/registry.yml — ¿qué roles se activan en este proyecto? (no todos son obligatorios)

## Backlog del proyecto
- [ ] <tarea 1>
- [ ] <tarea 2>

## Convención
Cada tarea que un agente tome de acá debe, al completarse, dejar rastro en
`graph/history/` — no alcanza con tildarla acá.
