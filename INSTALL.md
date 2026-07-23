# graph-init — instalación

Plugin que instala el patrón **GRAPH** en cualquier proyecto, disponible
como plugin nativo tanto para **Claude Code** (y Antigravity) como para
**Codex CLI**. Este documento es solo sobre cómo instalarlo — la explicación
de qué es GRAPH está en el [README](./README.md), y una vez instalado en
`.agents/graph/README.md`.

## Requisitos

- Claude Code (o Antigravity), **o** Codex CLI con soporte de plugins
  (marketplace de plugins, disponible desde Codex CLI 0.117+).
- Python 3 (para el indexador propio — no hace falta instalar nada más,
  viene con la librería estándar).

## Instalación en Claude Code / Antigravity

### 1. Agregar el marketplace

```
/plugin marketplace add jorge-repossi-tsoft/graph-init
```

(o la URL completa del repo, si tu versión de Claude Code lo pide así)

### 2. Instalar el plugin

```
/plugin install graph-init@graph-init
```

### 3. Recargar

```
/reload-plugins
```

(o reiniciá la sesión de Claude Code/Antigravity si ese comando no está
disponible en tu versión)

### 4. Confirmar que quedó instalado

Escribí `/graph-init:` en el chat — te tiene que aparecer
`/graph-init:graph-init` en la lista de comandos disponibles. Si no
aparece, el reload no tomó el plugin — repetí el paso 3.

### 5. Correrlo en tu proyecto

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

## Instalación en Codex CLI

Codex CLI tiene su propio marketplace de plugins (`.codex-plugin/`), con
manifest y hooks propios que este repo también incluye — no es el mismo
mecanismo que Claude Code, pero el flujo de uso es equivalente.

```
/plugin marketplace add jorge-repossi-tsoft/graph-init
/plugin install graph-init@graph-init
```

Luego corré el bootstrap parado en la raíz de tu proyecto:

```
/graph-init --mode=greenfield
```

o `--mode=brownfield [--migrate]` igual que en Claude Code.

**Nota:** esta ruta se armó y se validó contra la documentación oficial de
Codex (schema de `plugin.json`/`marketplace.json`/`hooks.json`), pero no se
probó todavía instalando de punta a punta en una sesión real de Codex CLI —
si el nombre exacto del comando después de instalar aparece distinto (por
ejemplo namespaced, `graph-init:graph-init`), fijate con `/skills` o
`/plugins` qué nombre te asignó y contanos para ajustar la doc. El
`PreToolUse`/`PostToolUse` de Codex solo intercepta `apply_patch` y `Bash`
(no hay `MultiEdit`/`NotebookEdit` separados como en Claude Code), así que
el circuit breaker cubre ediciones de archivo vía `apply_patch` igual.

## Alternativa: sin plugin system (versiones viejas de Codex, u otra herramienta)

Si tu herramienta no tiene sistema de plugins (o tenés una versión de Codex
anterior a la 0.117), el resultado de graph-init (el árbol `.agents/graph/`
más `AGENTS.md`) sigue siendo agnóstico: cualquier herramienta que lea
`AGENTS.md` lo aprovecha igual una vez que existe en el repo.

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
instalando alguno de los dos plugins (Claude Code o Codex CLI) — son los
que registran los hooks automáticamente. Corrido standalone con este
script, queda declarativo únicamente — ver
`.agents/graph/enforcement/README.md` para agregar un adaptador a tu
herramienta si necesitás que bloquee de verdad.

## Si algo no anda

- El comando no aparece → el reload no tomó el plugin, repetí el paso 3.
- Los hooks no parecen estar activos → confirmá que el `/plugin install`
  terminó sin errores; los 3 hooks (`claude-code-hook.sh`,
  `session-reset-hook.sh`, `stagnation-hook.sh`) se registran solos, no
  hace falta tocar `.claude/settings.json` a mano.
