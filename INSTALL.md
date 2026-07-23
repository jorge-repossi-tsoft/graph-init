# graph-init — instalación

Plugin de Claude Code que instala el patrón **GRAPH** en cualquier proyecto.
Este documento es solo sobre cómo instalarlo — la explicación de qué es
GRAPH está en el [README](./README.md), y una vez instalado en
`.agents/graph/README.md`.

## Requisitos

- Claude Code (o Antigravity, que lo soporta igual) instalado.
- Python 3 (para el indexador propio — no hace falta instalar nada más,
  viene con la librería estándar).

## 1. Agregar el marketplace

```
/plugin marketplace add jorge-repossi-tsoft/graph-init
```

(o la URL completa del repo, si tu versión de Claude Code lo pide así)

## 2. Instalar el plugin

```
/plugin install graph-init@graph-init
```

## 3. Recargar

```
/reload-plugins
```

(o reiniciá la sesión de Claude Code/Antigravity si ese comando no está
disponible en tu versión)

## 4. Confirmar que quedó instalado

Escribí `/graph-init:` en el chat — te tiene que aparecer
`/graph-init:graph-init` en la lista de comandos disponibles. Si no
aparece, el reload no tomó el plugin — repetí el paso 3.

## 5. Correrlo en tu proyecto

Parado en la raíz de tu proyecto (donde está tu `.git`):

```
/graph-init:graph-init --mode=greenfield
```

o, si el proyecto ya tiene código/historia:

```
/graph-init:graph-init --mode=brownfield
```

Si tu proyecto ya tenía un sistema `.agents/` de antes (progress.md,
tasks.md, system.md sueltos) y no querés perderlo:

```
/graph-init:graph-init --mode=brownfield --migrate
```

## Qué hace, en una línea

Arma `.agents/graph/` (spec, config del circuit breaker, gates de
aprobación, conocimiento del proyecto indexado automáticamente) y
`.agents/roles/` (planner/executor/reviewer). En modo `brownfield` además
indexa el código real del repo (con un indexador propio del plugin, sin
depender de nada externo) y reconcilia el historial de git.

Nunca pisa archivos que ya existan — si algo ya está, lo salta y te avisa.

## Alternativa: sin Claude Code ni Antigravity (Codex, u otra herramienta)

Codex (y cualquier herramienta sin sistema de plugins propio) no puede
correr `/plugin marketplace add` — eso es específico de Claude Code. Pero el
resultado de graph-init (el árbol `.agents/graph/` + `AGENTS.md`) es
agnóstico: cualquier herramienta que lea `AGENTS.md` lo aprovecha igual una
vez que existe en el repo.

Para generarlo sin pasar por ningún plugin:

1. Cloná este repo en cualquier lado (no hace falta que sea junto al
   proyecto que vas a inicializar):

```bash
git clone https://github.com/jorge-repossi-tsoft/graph-init.git
```

1. Parado en la raíz de tu proyecto, corré el bootstrapper standalone
   (mismo contrato que el comando de plugin — nunca pisa archivos
   existentes, corre el indexador real en brownfield, etc.):

```bash
python3 /ruta/a/graph-init/scripts/graph-init.py . --mode=greenfield
```

o

```bash
python3 /ruta/a/graph-init/scripts/graph-init.py . --mode=brownfield --migrate
```

1. Abrí el proyecto con Codex (o la herramienta que uses) normalmente —
   va a leer `AGENTS.md` como cualquier otro.

**Límite honesto:** el circuit breaker (`enforcement/`) solo se activa
solo con el plugin de Claude Code. Corrido standalone, queda declarativo
únicamente — ver `.agents/graph/enforcement/README.md` para agregar un
adaptador a tu herramienta si necesitás que bloquee de verdad.

## Si algo no anda

- El comando no aparece → el reload no tomó el plugin, repetí el paso 3.
- Los hooks no parecen estar activos → confirmá que el `/plugin install`
  terminó sin errores; los 3 hooks (`claude-code-hook.sh`,
  `session-reset-hook.sh`, `stagnation-hook.sh`) se registran solos, no
  hace falta tocar `.claude/settings.json` a mano.
