# GRAPH — en criollo

GRAPH es una forma de organizar cómo un agente de IA (Claude Code, Cursor,
Antigravity, el que sea) trabaja en tu proyecto, para que no actúe "a lo
loco" y siempre quede algo de vos en el medio antes de que pase algo
importante.

No es una librería ni un framework. Es más parecido a una checklist de
buenas prácticas — como SOLID para el código, pero para cómo se comporta un
agente autónomo.

## La idea en una frase

> El agente no actúa por las suyas: primero consulta lo que ya sabe del
> proyecto, y las decisiones importantes pasan por vos antes de ejecutarse.

## Las 5 partes (GRAPH es la sigla)

**G — Grounded (con los pies en la tierra)**
El agente mira el estado real del proyecto antes de proponer algo, no
inventa ni "recuerda mal" de la conversación. Toda esa info vive en
`graph/knowledge/`.

**R — Reviewable (revisable)**
Los cambios que importan no se aplican solos. Quedan esperando en
`graph/gates/pending/` hasta que alguien (vos) los apruebe. Nada de "el
agente decidió y ya está".

**A — Agnostic (no depende de una sola herramienta)**
Si mañana cambiás de Claude Code a Cursor o a lo que sea, el proyecto sigue
funcionando igual. Todo vive en archivos de texto comunes, no en algo
propietario de una sola app.

**P — Persistent (no se olvida de una sesión a la otra)**
Lo que se habló y se decidió ayer, queda escrito en
`graph/sessions/progress.md` y `tasks.md`. La próxima sesión arranca
sabiendo qué pasó, no repite todo de cero.

**H — Hierarchical (organizado, no todo tirado junto)**
El conocimiento del proyecto se agrupa en comunidades y nodos (como carpetas
temáticas dentro de `knowledge/`), y cada cambio queda con su historial
pegado — no en un log aparte que nadie mira.

## El plus que no entra en la sigla: Circuit Breaker

Es el freno de mano. Si el agente se pone a hacer 25 cosas seguidas sin
parar, o repite lo mismo una y otra vez sin avanzar, esto lo corta solo y
te avisa. No hace falta que vos estés mirando todo el tiempo para que se
frene un loop descontrolado.

Vive en `graph/circuit-breaker.yml` (la configuración) + tres scripts que
realmente lo hacen cumplir (`graph/enforcement/`, aunque si instalaste esto
como plugin, esos scripts ya vienen activos solos, no hace falta tocar
nada).

## ¿Qué carpeta hace qué?

```
.agents/
├── graph/
│   ├── GRAPH.md              → la spec completa, para leer con más calma
│   ├── circuit-breaker.yml   → configuración del freno de mano
│   ├── knowledge/            → lo que el agente sabe del proyecto
│   ├── gates/
│   │   ├── pending/          → propuestas esperando tu aprobación
│   │   └── approved/         → lo que ya aprobaste
│   ├── sessions/
│   │   ├── progress.md       → qué se hizo, sesión por sesión
│   │   └── tasks.md          → qué falta hacer
│   └── enforcement/          → los scripts que hacen cumplir el freno de mano
└── roles/                    → qué puede hacer cada "sombrero" (planner, executor, reviewer)
```

## ¿Cómo lo uso día a día?

En la práctica, casi todo pasa solo:

1. Le pedís algo al agente.
2. Si es algo chico (leer, proponer un plan), lo hace directo.
3. Si es algo que cambia código o config importante, te va a dejar una
   propuesta en `gates/pending/` en vez de aplicarla sola.
4. Vos la mirás, la aprobás o la rechazás.
5. Si en algún momento el agente se traba en un loop, el circuit breaker
   corta solo y te avisa — no sigue insistiendo para siempre.

Eso es todo. El resto (`GRAPH.md`, `policy.yml`, `registry.yml`) es la letra
chica para cuando quieras ajustar algo puntual.
