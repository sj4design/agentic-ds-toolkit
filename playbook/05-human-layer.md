---
title: "Phase E — Human Layer / Governance Social"
status: "v1 — de-placeholder 2026-07-23: el contenido propio de sources/07 y 08 (incidente de la escala amarilla, la costura designops/devops, la redefinición del rol) ya está transcrito, no solo nombrado. Las 6 secciones siguen siendo outline a instanciar."
intent: "framing"
audience: "system steward + leadership + IC contributors"
self_contained: true
---

# Phase E — Capa humana / Governance Social

> Esta es la fase **cross-cutting** del roadmap (paralela a Fases A-D). Sin un humano accountable, los artifacts técnicos producidos quedan huérfanos.
>
> Cita central que define la fase: *"The infrastructure I've documented in this series is reproducible. [...] The organizational shift, getting a team to encode its knowledge and accept accountability for what gets encoded, that's harder than any of the technical work."* — Cristian Morales, `sources/08-...:82`. Este capítulo llena exactamente el gap que Cristian admite no tener (`sources/08-...:74`).

## El incidente de la escala amarilla — el caso fundacional

Este capítulo se apoyaba en "la yellow scale story" para justificar su rúbrica de steward, pero
nunca la contaba. Aquí está, porque **la secuencia es la metodología**.

Una desarrolladora construía un mensaje de aviso y notó que el contraste del texto sobre el fondo
amarillo parecía mal (`sources/07-...:22`). Podría haber ajustado el color a ojo y seguir. **No lo
hizo: vino a preguntar** (`sources/07-...:24`). Revisaron juntos la implementación —tokens
correctos, mapeo correcto, uso correcto— y el token auditor confirmó que el componente estaba bien
(`sources/07-...:26`).

> ### El error era suyo, del propio diseñador del sistema.
> — `sources/07-...:28`

En algún momento había estropeado los valores de la escala primitiva de amarillo
(`sources/07-...:30`). Lo interesante no es el arreglo, que fue trivial (`sources/07-...:32`). Es
la **secuencia de seis pasos**, que es un diagnóstico reproducible (`sources/07-...:34`):

1. Una desarrolladora detecta un problema visual.
2. **No parchea alrededor.**
3. Lo traza hacia atrás a través del sistema.
4. El auditor confirma que la implementación es correcta.
5. El error apunta a la capa de primitivos.
6. La capa de primitivos apunta a su dueño.

> "The system didn't surface an error. It surfaced whose error it was. Mine."
> — `sources/07-...:80`

**El contraste con el flujo tradicional** es el argumento entero: ahí ese fallo de contraste se
detecta —si se detecta— en QA, y *"nobody knows where the actual decision went wrong because the
chain between decision and execution has too many links"* (`sources/07-...:36`). El sistema
agéntico no evita el error; **acorta la cadena hasta que el error tiene dueño**.

Por eso la señal de steward latente no es "sabe mucho de tokens", sino **traza en vez de
parchear**. Y por eso la accountability es un prerrequisito y no un valor: sin alguien que acepte
que el error apunte hacia él, la cadena vuelve a alargarse.

## La costura donde designops se encuentra con devops

Sección que faltaba entera, y es el aporte conceptual más original de la fuente.

> "A design token governance rule and a CI pipeline linting check serve the same structural
> purpose: enforce decisions at the point of execution."
> — `sources/07-...:56`

La distinción que se sigue: *"My token auditor doesn't just check that tokens exist (that's a
linter). It checks whether the relationships between tokens violate the design intent (that's
governance)"* (`sources/07-...:58`). Es la misma frontera linter/governance del capítulo
[04-enforce](04-enforce.md), pero **enunciada desde el lado humano**: no es que la herramienta sea
más lista, es que alguien tuvo que articular cuál es la intención.

No se trata de fundir las dos disciplinas —siguen teniendo necesidades y expertise distintos
(`sources/07-...:60`)—, sino de reconocer que **el trabajo está en la costura**. Ese es el terreno
del steward: ni diseño puro ni ingeniería pura.

> ⚠️ **Origen doble, atribución a medias.** Este ejemplo (`--foreground-muted` usado para texto de
> contenido) aparece en `sources/07` **y** en `sources/06`. El playbook lo atribuía solo a 06. Se
> deja constancia porque la costura es, en 07, un argumento sobre el *rol*; en 06, sobre la
> *herramienta*.

## Qué rol es este, exactamente (Cristian Part 8)

La rúbrica de la §1 gana precisión con la definición del propio autor. Y arranca con una tesis
contraintuitiva que un capítulo de capa humana no puede omitir:

> "the companies that get this right will hire more designers, more PMs, more QA specialists. Not fewer."
> — `sources/08-...:40`

El razonamiento: cuando la velocidad de desarrollo sube 3-4x, los roles de alrededor tienen que
acompañar o quemas el sistema (`sources/08-...:42`). **Es un argumento de headcount para el
capítulo de buy-in**, no un consuelo.

Pero "más diseñadores" no significa más del mismo rol (`sources/08-...:44`). El trabajo que
describe *"sits in a space that requires design judgment, systems thinking, and enough technical
fluency to work the interface between design and code"* — **tres competencias**, y no mapea ni al
product designer tradicional ni al rol clásico de design systems.

**Y el corolario que amplía el mandato:** la fluidez con IA convierte a expertos de dominio en
constructores. Su Head of Operations —experta en contratación clínica, no desarrolladora ni en vías
de serlo— construyó su propia herramienta de proyecciones: *"a domain expert whose background gave
her the ability to describe what she needed, and AI fluency gave her the ability to encode that
knowledge into a tool"* (`sources/08-...:62`). El steward no es el único que encoda; es quien
garantiza que lo encodado sea coherente.

## La prescripción (y por qué es la misma en las dos fuentes)

Las dos partes convergen en un mismo "qué hacer", y es lo más accionable del capítulo:

> "Teams need to sit down and set these rules. Surface errors when they catch them."
> — `sources/07-...:70`

Ampliado en Part 8: articular las reglas que se venían siguiendo de forma implícita, **sacar los
errores a la superficie cuando se detectan**, y acordar qué es correcto y qué es una excepción —
porque si no, *"AI scales whatever it finds. Including errors. Including drift"* (`sources/08-...:80`).

Ese tercer punto —**acordar qué es correcto y qué es excepción**— es la entrada directa del ritual
de excepciones de la §4. No es un trámite de gobernanza: es lo que impide que cada desviación
legítima se registre como error y que cada error se justifique como excepción.

## Alcance honesto de esta metodología

El autor declara sus condiciones privilegiadas (`sources/08-...:68`), y conviene recogerlas porque
el mandato de este repo es **replicabilidad**: diseñador único, acceso directo a ingeniería,
empresa pequeña, adopción impulsada desde arriba, sin comités.

Ninguna de esas condiciones es la de un equipo grande con múltiples marcas. La infraestructura es
reproducible; **el cambio organizativo es la parte difícil**, y es exactamente lo que este capítulo
—y la Fase E— tienen que resolver para equipos que no sean el del autor.

## 6 secciones obligatorias

(Per Loop F4.8 — outline específico para Phase E, no genérico chapter template.)

### 1. Steward role + selection rubric `[intent: framing]`

**Output esperado**: rubric concreta para identificar steward + role description shippeable a HR.

#### Signals de latent steward
- Reporta inconsistencias hacia el sistema (vs hardcodea alrededor) — cita Cristian P7 yellow scale story.
- Conoce el "por qué" de decisiones, no solo el "qué".
- Tiene authority de decisión sobre el DS, no solo permiso de ejecución.
- "Encode decisions and know" vs "hand off and hope" — `sources/07-...:82`.

#### Disqualifying patterns
- IC ocupado sin decision authority.
- Manager sin hands-on contact.
- Asignación por seniority sin volunteering.

#### Role description template
Escrito para que un manager lo apruebe sin contexto adicional:
- N hrs/week formalmente asignadas (ver §6 bandwidth model).
- Accountability scope: tokens / components / rules / orchestration.
- Decision rights: solo, dual, escalate-to-committee — clarificar.
- Reporting line: a quien escala edge cases (EC-1, EC-2 del roadmap).

### 2. Fluency assessment v0 `[intent: guideline]`

**Output esperado**: rubric pública, directional (no precision), based en Anthropic 4Ds + observable behaviors.

(Ver `framework/assessments/4ds-rubric.md` cuando exista — placeholder por ahora.)

#### Tabla rubric v0

| Dimensión | 1 (low) | 2 (mid) | 3 (high) |
|-----------|---------|---------|----------|
| **Delegation** | Asks for review at every step | Asks at major checkpoints | Self-validates with evals before review |
| **Description** | Vague intent in prompts | Includes context but mixed quality | Structured intent + relevant context |
| **Discernment** | Accepts AI output as-is | Catches obvious errors | Catches subtle drift, traces root cause |
| **Diligence** | Skips validation | Does spot checks | Runs full audit before commit |

#### Política importante
**NO usar este assessment para performance review durante el primer ciclo de adoption** (ver §5 lateral resistance). Es desarrollo, no juicio.

### 3. Cultivation pathway `[intent: workflow]`

**Output esperado**: progresión 6-12 meses Shadow → Co-own → Own.

| Fase | Mes | Sub-rol | Output esperado |
|------|-----|---------|-----------------|
| **Shadow** | 1-2 | Observador | Asistir a decisiones, NO firmar. Lee SYNTHESIS, ADRs, references |
| **Co-own** | 3-6 | Co-firmante | Co-decidir con steward senior, dual signoff en ADRs y rules |
| **Own** | 7-12 | Steward | Firmar solo, escalar a comité solo edge cases EC-1..EC-7 |

Esto trata accountability como **cultivable**, no innate. Resuelve Loop F4.2: el framework no debería asumir traits, debe desarrollarlos.

### 4. Exception management ritual `[intent: constraint]`

**Output esperado**: política activa, no passive list.

Conexión con Fase C: las `exceptions[]` whitelist en `framework/rules/*.md` NO son lista pasiva. Son **artifact accountability**:

- **Steward owns recurring review** (mensual o por release): repasa exceptions activas.
- **Métrica**: closed exceptions / quarter — signal de que el steward cierra deuda, no acumula.
- **Trigger automático**: si una exception lleva 2+ minor versions sin migration plan, CI emite warning. Steward escala.
- **Public exception ritual**: cuando alguien legítimamente NO puede usar el sistema (gap conocido), su reporte es público + se acredita como contribution.

Esto convierte exception management de policy passive a ritual activo. Ver `references/cristian-morales/token-audit/01-tokenization-layers.md` Insights A.2 + A.3.

### 5. Lateral adoption patterns `[intent: framing]`

**Output esperado**: cómo manejar resistance peer-level (gap explícito de Cristian P8 L74).

Grace 10 cubre buy-in para leadership. Cristian Part 8 L74 admite gap para resistance peer-level. Patrones para ese gap:

- **Paired authoring**: high-fluency + low-fluency couplings forzados durante 4-6 semanas.
- **Opt-in pilot before mandatory rollout**: 3-5 voluntarios prueban el sistema antes de rollout completo.
- **Decoupling fluency from performance review** durante primer ciclo (≤6 meses) — ver §2 política.
- **Public exception ritual** (§4 arriba) — convierte "no pude usarlo" en contribution.
- **No surprise rollout**: cualquier mandatory adoption tiene 30 días de aviso + onboarding session.

### 6. Cost / bandwidth model `[intent: guideline]`

**Output esperado**: brackets directional, explícitamente marked v0.

| Bracket | Tiempo | Componentes ~baseline | Steward FTE |
|---------|--------|-----------------------|-------------|
| **Bootstrap** | semanas 1-12 | 0 → ~35 con metadata | 50-75% (intensivo) |
| **Steady-state** | post-bootstrap | 35-100 components | 15-25% (mantenimiento) |
| **Crisis/migration** | brand pivot, framework migration | N/A | 50%+ (back to bootstrap) |

**Caveat explícito**: `[v0 estimate, no source numérico — derivado del sistema observado en references/cristian-morales/. Calibrar con case studies propios.]`

Mejor honest+directional que ausente. Cierra parcialmente GAP 4 SYNTHESIS.

## Sequencing — quando arranca cada sub-deliverable

| Sub-deliverable | Cuándo | Bloqueante? |
|-----------------|--------|-------------|
| §1 Steward identification | ANTES de Fase A start | ✅ Gate |
| §2 Fluency rubric v0 | ANTES de Fase A start | ✅ Gate (rubric existe, evaluación NO) |
| §3 Cultivation pathway | Mes 1, paralelo a Fase A | ❌ Parallel |
| §4 Exception ritual | Cuando Fase C ship rules | ❌ Parallel |
| §5 Lateral adoption | Cuando Fase A genera primer artifact public | ❌ Parallel |
| §6 Bandwidth model | Definido aquí (placeholder), refinado con case studies | ❌ Parallel |

## Hand-off post-Fase-E

Phase E está done cuando los 6 quantified criteria del roadmap pasan:

- [ ] Steward identified by name with N hrs/week formally allocated and signed off by manager.
- [ ] Cultivation plan documented with 6-12 month progression.
- [ ] At least N implicit rules articulated to `framework/rules/draft/*.md` via workshop protocol.
- [ ] 4Ds rubric v0 published at `framework/assessments/4ds-rubric.md`.
- [ ] Lateral adoption playbook documented (this file).
- [ ] Exception ritual scheduled (recurring monthly review on calendar).
- [ ] Bandwidth bracket selected and committed for current phase.

## Referencias

- AI-READY-ROADMAP.md Fase E (versión expandida en commit 1b137cd).
- `sources/07-cristian-morales-part-7-the-human-layer-in-agentic-design-systems.md`.
- `sources/08-cristian-morales-part-8-what-building-agentic-systems-taught-me.md`.
- `sources/10-grace-han-how-to-get-buy-in-for-an-ai-ready-design-system.md`.
- Loop findings F4.1-F4.8 (Analyst 4, Human Layer Strategist).

## Status

Placeholder con outline operacional completo. Se llenará con casos concretos del primer case study + workshop logs reales.
