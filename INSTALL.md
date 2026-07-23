# graph-init — instalación

Plugin que instala el patrón **GRAPH** en cualquier proyecto. Este
documento es solo sobre cómo instalarlo — la explicación de qué es GRAPH
está en el [README](./README.md), y una vez instalado en
`.agents/graph/README.md`.

GRAPH no está atado a una sola herramienta de IA. Hay tres formas de
instalarlo — elegí la que corresponda a lo que usás:

## Opción 1 — Universal (funciona con cualquier LLM/agente, sin plugin system)

```bash
git clone https://github.com/jorge-repossi-tsoft/graph-init.git /tmp/graph-init
cd /tu/proyecto
python3 /tmp/graph-init/scripts/graph-init.py . --mode=greenfield
# o, si el proyecto ya tiene código:
python3 /tmp/graph-init/scripts/graph-init.py . --mode=brownfield --migrate
```

Esto arma toda la estructura `.agents/` y el bridge `AGENTS.md`/`CLAUDE.md`
usando solo Python 3 — nada de plugins, nada específico de una
herramienta. Cualquier agente que lea `AGENTS.md` (Claude Code, Codex CLI,
Cursor, lo que sea) va a encontrar el contexto igual.

**Limitación honesta:** el circuit breaker (freno de mano que *bloquea* de
verdad acciones del agente) necesita que tu herramienta tenga un sistema de
hooks compatible. Instalado así, `circuit-breaker.yml` queda como contrato
documentado que el agente puede leer y respetar, pero no hay enforcement
forzado — para eso, ver la Opción 2 o la 3.

## Opción 2 — Plugin de Claude Code (agrega enforcement real del circuit breaker)

Requisitos: Claude Code (o Antigravity) instalado, Python 3.

### 1. Agregar el marketplace

```
/plugin marketplace add jorge-repossi-tsoft/graph-init
```

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
/graph-init:graph-init --mode=brownfield --migrate
```

## Opción 3 — Plugin de Codex CLI (agrega enforcement real del circuit breaker en Codex)

Requisitos: Codex CLI, Python 3.

### 0. Si no tenés Codex CLI instalado

```bash
npm install -g @openai/codex
codex --version
```

(el paquete correcto es `@openai/codex`, no `codex` a secas — ese último
es un proyecto viejo sin relación con OpenAI)

### 1. Agregar el marketplace

```bash
codex plugin marketplace add jorge-repossi-tsoft/graph-init
```

### 2. Instalar el plugin

```bash
codex plugin add graph-init@graph-init
```

### 3. Iniciar una sesión nueva

Las skills que trae el plugin quedan disponibles recién al arrancar una
sesión nueva de Codex — cerrá la actual si estaba abierta.

### 4. Correrlo en tu proyecto

Codex no tiene comandos slash tipo `/graph-init:graph-init` — la lógica
quedó empaquetada como una Skill. Parado en la raíz de tu proyecto,
pedíselo en lenguaje natural:

> "Instalá el patrón GRAPH acá — modo brownfield, con migrate"

Codex debería reconocer y usar la skill `graph-init` sola.

**Comandos confirmados contra Codex CLI 0.142.0** (con `codex plugin
marketplace add --help` / `codex plugin add --help`) — si tu versión da
error de "subcomando no reconocido", corré esos mismos `--help` para
confirmar la sintaxis de tu versión puntual.

## Qué hace, en una línea

Arma `.agents/graph/` (spec, config del circuit breaker, gates de
aprobación, conocimiento del proyecto indexado automáticamente) y
`.agents/roles/` (planner/executor/reviewer). En modo `brownfield` además
indexa el código real del repo (con un indexador propio del plugin, sin
depender de nada externo) y reconcilia el historial de git.

Nunca pisa archivos que ya existan — si algo ya está, lo salta y te avisa.

## Si algo no anda

- El comando/skill no aparece → el reload/sesión nueva no tomó el plugin,
  repetí el paso correspondiente.
- Los hooks no parecen estar activos → confirmá que la instalación del
  plugin terminó sin errores; los 3 hooks (`claude-code-hook.sh`,
  `session-reset-hook.sh`, `stagnation-hook.sh`) se registran solos, no
  hace falta tocar `settings.json`/`config.toml` a mano.
- Codex: si `codex plugin marketplace add` o `codex plugin add` dan
  "unrecognized subcommand", tu versión tiene otra estructura — corré
  `codex plugin --help` para ver los subcomandos reales disponibles.
