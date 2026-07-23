# graph-init — instalación

Plugin de Claude Code que instala el patrón **GRAPH** en cualquier proyecto.
Este documento es solo sobre cómo instalarlo — la explicación de qué es
GRAPH está en el [README](./README.md), y una vez instalado en
`.agents/graph/README.md`.

GRAPH no está atado a una sola herramienta de IA. Hay dos formas de
instalarlo:

## Opción 1 — Universal (funciona con cualquier LLM/agente)

```bash
git clone https://github.com/SudacaDev/graph-init.git /tmp/graph-init
cd /tu/proyecto
bash /tmp/graph-init/install.sh --mode=greenfield
# o, si el proyecto ya tiene código:
bash /tmp/graph-init/install.sh --mode=brownfield [--migrate]
```

Esto arma toda la estructura `.agents/` y el bridge `AGENTS.md`/`CLAUDE.md`
usando solo bash + Python 3 (para el indexador) — nada de plugins, nada
específico de una herramienta. Cualquier agente que lea `AGENTS.md` (Claude
Code, Codex CLI, Cursor, lo que sea) va a encontrar el contexto igual.

**Limitación honesta:** el circuit breaker (freno de mano que *bloquea* de
verdad acciones del agente) necesita que tu herramienta tenga un sistema de
hooks compatible. Instalado así, `circuit-breaker.yml` queda como contrato
documentado que el agente puede leer y respetar, pero no hay enforcement
forzado — para eso, ver la Opción 2.

## Opción 2 — Plugin de Claude Code (agrega enforcement real del circuit breaker)

Requisitos: Claude Code (o Antigravity) instalado, Python 3.

### 1. Agregar el marketplace

```
/plugin marketplace add SudacaDev/graph-init
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

## Si algo no anda

- El comando no aparece → el reload no tomó el plugin, repetí el paso 3.
- Los hooks no parecen estar activos → confirmá que el `/plugin install`
  terminó sin errores; los 3 hooks (`claude-code-hook.sh`,
  `session-reset-hook.sh`, `stagnation-hook.sh`) se registran solos, no
  hace falta tocar `.claude/settings.json` a mano.
