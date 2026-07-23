---
title: "Phase E — Human Layer / Governance Social"
status: "placeholder — addresses Loop F4.8 (chapter outline) + Phase E expansion in roadmap"
intent: "framing"
audience: "system steward + leadership + IC contributors"
self_contained: true
---

# Phase E — Capa humana / Governance Social

> Esta es la fase **cross-cutting** del roadmap (paralela a Fases A-D). Sin un humano accountable, los artifacts técnicos producidos quedan huérfanos.
>
> Cita central que define la fase: *"The infrastructure I've documented in this series is reproducible. [...] The organizational shift, getting a team to encode its knowledge and accept accountability for what gets encoded, that's harder than any of the technical work."* — Cristian Morales, `sources/08-...:82`. Este capítulo llena exactamente el gap que Cristian admite no tener (`sources/08-...:74`).

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
