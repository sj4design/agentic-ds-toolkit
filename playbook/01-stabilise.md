---
phase: "Pre-Fase / Step 0"
audience: "Designer + tech lead que empiezan a evaluar AI-readiness de su DS"
prerequisites: "Acceso al repo del DS; permiso para hacer auditoría informal"
primary_intent: workflow
secondary_intents: [framing]
sources:
  - sources/10-grace-han-how-to-get-buy-in-for-an-ai-ready-design-system.md (SAFE framework)
  - sources/11-grace-han-ai-ready.md (Phase 1 Stabilise)
  - sources/07-cristian-morales-part-7-the-human-layer-in-agentic-design-systems.md (capa humana — Step 0.3)
  - sources/08-cristian-morales-part-8-what-building-agentic-systems-taught-me.md (AI fluency 4Ds — Step 0.2)
status: "Draft — contenido escrito; Gotchas a llenar con aplicación real fuera de color-ramp"
---

# 01 — Stabilise: diagnosis estructural antes de cualquier inversión AI

## Cita verbatim

> "Phase 1: Stabilise (stop the chaos)"
> — `sources/11-grace-han-ai-ready.md:79`

> "AI amplifies whatever structure already exists. If the structure is mature, AI becomes leverage. If the structure is weak, AI becomes volatile."
> — `sources/10-grace-han-how-to-get-buy-in-for-an-ai-ready-design-system.md:152`

## [INTERPRETACIÓN]

Antes de invertir en infra AI, **diagnosticar la madurez estructural**. La dimensión más débil determina la primera inversión, no la moda tecnológica. Tres assessments componen Step 0:

- **0.1 SAFE diagnosis** (estructural) — Grace Han
- **0.2 AI fluency baseline** (capacidad humana) — Cristian Part 8
- **0.3 Accountability map** (ownership social) — Cristian Part 7

## Patrón replicable

### 0.1 SAFE diagnosis (questionnaire)

Para cada dimensión, score Low / Medium / High con evidencia:

#### S — Structure (¿el sistema está estable?)
- [ ] Existe un Single Source of Truth canónico por dominio (1 archivo, 1 SSOT)
- [ ] Cero duplicaciones detectables del SSOT en código consumer
- [ ] Ownership claro de cada artefacto (quién decide cambios)
- [ ] Convención de naming consistente

**Score**: 4/4 = High · 2-3/4 = Medium · 0-1/4 = Low

#### A — Alignment (¿la semántica es consistente cross-platform/cross-product?)
- [ ] Mismo token name en web vs mobile vs print (donde aplique)
- [ ] Mismo significado semántico cross-region (si el DS opera multi-mercado)
- [ ] Tokens shared via build process, no duplicados manualmente
- [ ] Cambios en SSOT propagan sin manual edits cross-platform

**Score**: 4/4 = High · 2-3/4 = Medium · 0-1/4 = Low · N/A si mono-plataforma

#### F — Formalisation (¿decisiones como contracts o como prosa?)
- [ ] Reglas críticas existen como schema validable (no solo prose)
- [ ] Componentes tienen contracts axis-indexed (no solo "estados visuales")
- [ ] Cambios en reglas versionados (semver o equivalente)
- [ ] Deprecation explícita, no eliminación silenciosa

**Score**: 4/4 = High · 2-3/4 = Medium · 0-1/4 = Low

#### E — Enforcement (¿validación automática en pipeline?)
- [ ] CI corre audit suite en cada PR
- [ ] Audit suite incluye invariantes runtime (no solo lint estático)
- [ ] Mutation testing valida que los audits no tienen blind spots
- [ ] Drift detectable automáticamente (specHash o equivalente)

**Score**: 4/4 = High · 2-3/4 = Medium · 0-1/4 = Low

**Resultado**: la dimensión más baja = primera inversión. Si S está bajo, no invertir en E aún (no hay qué enforce). Si E está alto pero F bajo, la enforcement amplifica decisiones poco formalizadas (riesgo: scale errors).

### 0.2 AI fluency baseline (4D Anthropic)

Per Cristian Part 8 (`sources/08-...:56`), Anthropic publica un AI Fluency Index con 4 dimensiones:

- **Delegation** — ¿cuán bien delega el equipo trabajo a AI?
- **Description** — ¿cuán bien describen lo que necesitan?
- **Discernment** — ¿cuán bien discriminan calidad del output?
- **Diligence** — ¿cuán bien mantienen rigor en el uso?

**Rubric propuesto** (work-in-progress — el instrumento concreto NO está publicado en las fuentes):

Para cada dimensión, score 1-5 por persona del equipo:
- 1 = nunca usa AI / no confía
- 2 = uso ocasional one-shot prompts
- 3 = uso recurrente para tareas concretas, no estructural
- 4 = encoda parte de su trabajo en skills/rules; confía + verifica
- 5 = diseña environments (Cristian Part 7 `:74`) — encode decisions, accountable for output

**Resultado**: dimensión más baja del equipo = training focus. Si Discernment promedio < 3, invertir en infra antes que en velocidad (riesgo de scaled errors).

> ⚠ Cristian explícita el gap (`sources/08-...:74`): _"I don't know what the politics of measuring AI fluency look like in an organization where some people will score low and feel threatened."_ — usar este rubric con cuidado político.

### 0.3 Accountability map

Per Cristian Part 7 (`sources/07-...:82`): _"Most designers hand off decisions and hope. I encode decisions and know. The tool makes that possible. The willingness to be accountable makes it real."_

**Template**:

| Decisión | Owner | Encoded? | Auditable? |
|---|---|---|---|
| _ej: rangos de contraste por step_ | Designer X | sí — schema en `<file>` | sí — audit `<X>` |
| _ej: cuándo un componente entra al canon_ | Tech lead Y + Designer X | no — implícito en code review | no |

Cualquier "Encoded? = no" + "Auditable? = no" = riesgo de scaled errors. Step 0.3 produce la lista; siguientes fases la cierran.

## Ejemplo concreto (case study color-ramp)

Ver **`case-studies/color-ramp/ROADMAP.md`** §"Estado inicial". Color-ramp SAFE inicial (2026-04-24):
- S = High (palette-hsl.js SSOT establecido)
- A = N/A (mono-plataforma web)
- F = Low (recipes como prose en MD, no schema)
- E = Very Low (changelog informal, sin CI)

→ Primera inversión: F (Fase C — reglas como data). Eso es lo que de facto se hizo: Phase C implementada con 286 atomic rules + 3 constraints.

## Gotchas / edge cases

- _A llenar conforme se ejecuta el primer Step 0 fuera de color-ramp_

## Checklist replicable

1. [ ] Completar SAFE questionnaire (S/A/F/E) con evidencia por checkbox
2. [ ] Score AI fluency baseline para cada persona del equipo (consent first)
3. [ ] Documentar accountability map (decisión → owner → encoded? → auditable?)
4. [ ] Identificar dimensión SAFE más baja → primera fase a invertir
5. [ ] Identificar AI fluency dimension más baja del equipo → training focus paralelo
6. [ ] Registrar baseline en `case-studies/<your-system>/STATE.md` o equivalente para tracking de progreso

## Referencias

- Grace Han SAFE framework: `sources/10-grace-han-how-to-get-buy-in-for-an-ai-ready-design-system.md:86-108`
- Grace Han Phase 1: `sources/11-grace-han-ai-ready.md:79-115`
- AI fluency 4Ds: `sources/08-cristian-morales-part-8-what-building-agentic-systems-taught-me.md:56`
- Accountability framing: `sources/07-cristian-morales-part-7-the-human-layer-in-agentic-design-systems.md:82`
- Caveat politics: `sources/08-cristian-morales-part-8-what-building-agentic-systems-taught-me.md:74`
