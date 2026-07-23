---
phase: "Cross-cutting — Buy-in organizacional"
audience: "Design lead + DS architect que necesitan vender la inversión AI-ready a leadership"
prerequisites: "Step 0 SAFE diagnosis completado (provee scorecard concreto para presentar)"
primary_intent: framing
secondary_intents: [workflow]
sources:
  - sources/10-grace-han-how-to-get-buy-in-for-an-ai-ready-design-system.md (SAFE framework + risk framing)
  - sources/08-cristian-morales-part-8-what-building-agentic-systems-taught-me.md (evidencia operativa 3-4x throughput)
status: "Draft — contenido escrito; Gotchas a llenar con aplicación real fuera de color-ramp"
---

# 99 — Buy-in: cómo presentar la inversión AI-ready a leadership

## Cita verbatim

> "Output volume ➝ Variability ➝ Operational risk."
> — `sources/10-grace-han-how-to-get-buy-in-for-an-ai-ready-design-system.md:35`

> "An AI-ready design system is not a design upgrade. It is a risk management mechanism that enables safe acceleration."
> — `sources/10-grace-han-how-to-get-buy-in-for-an-ai-ready-design-system.md:40`

> "AI is a multiplier. It multiplies structure."
> — `sources/11-grace-han-ai-ready.md:35`

## [INTERPRETACIÓN]

Buy-in NO se vende como "design upgrade" (eso compite con feature work y pierde). Se vende como **risk management mechanism**. La cadena causal es:
1. AI velocity → output volume ↑
2. Output volume → variability ↑ (si la estructura es débil)
3. Variability → operational risk ↑
4. ∴ DS AI-ready = control layer para acelerar sin acumular deuda

Quien vende debe llegar con **evidencia operativa** (Cristian Part 8: head of data midió 3-4x throughput per developer). Sin métricas, el pitch es aspiracional y pierde contra prioridades inmediatas.

## Patrón replicable

### 99.1 — Framing de risk

Grace Han (`sources/10-...:23-27, :38-40`):
> "Then position the design system as the control layer. An AI-ready design system is not a design upgrade. It is a risk management mechanism that enables safe acceleration."

Estructura del pitch:
1. **Establecer el shift**: dev velocity está aumentando 3-4x con AI (cita Cristian Part 8 `:22` si tienes datos propios mejores)
2. **Articular el riesgo**: sin DS AI-ready, ese output multiplica inconsistencias, brand fragmentation, accessibility debt
3. **Posicionar el DS como control layer**: no es feature, es infraestructura de control
4. **Show data**: SAFE scorecard del DS actual + dimensión más débil = inversión prioritaria

### 99.2 — SAFE como prioritization tool

Per Grace Han (`sources/10-...:86-108`):
- **S — Structure**: ¿stable, SSOT, ownership claro?
- **A — Alignment**: ¿consistencia cross-platform/region?
- **F — Formalisation**: ¿decisions como contracts vs prose?
- **E — Enforcement**: ¿validación automática en pipeline?

La dimensión más baja = highest risk exposure. Esto convierte "AI-readiness" abstracto en **prioritised investment plan**.

Presentación a leadership:
- Tabla con SAFE actual del DS (4 dimensiones × Low/Medium/High)
- Risk if weak (Grace Han columna): "Fragmentación at scale" (A bajo), "Ambigüedad bajo automation" (F bajo), etc.
- Cost-of-inaction estimado (cuánto cuesta un brand-misalignment incident, un accessibility lawsuit, etc.)
- ROI inicial: dimensión más débil + qué cuesta moverla a Medium/High

### 99.3 — Capability como parte del pitch

Per Grace Han `:113` ("Make capability part of the case"): un riesgo subestimado es **capability** del equipo. AI fluency baja escala errores. El pitch debe incluir:
- AI fluency baseline del equipo (Step 0.2)
- Training plan asociado a la inversión
- Hiring/role redefinition si aplica (Cristian Part 8 `:42` — "the ratio between builders and support profiles was calibrated for a different speed")

### 99.4 — Datos para presentar

Si tienes métricas propias > usá esas. Si no, citas directionales de las fuentes:
- Cristian Part 2 (`:58`): "With infrastructure: 2x faster, 54% more accurate, zero false negatives. At basically the same token cost."
- Cristian Part 8 (`:22`): "Our development team is producing at 3x to 4x per developer compared to 2023."
- Grace Han (`sources/10-...:65`): "20-40% productivity gains... 30-50% of front-end budgets in refactoring."

## Ejemplo concreto

Grace Han trae un escenario cautelar listo para pitch (`sources/10-...:73-79`): una organización de retail global introdujo generación de interfaces asistida por IA en varios equipos regionales; en meses, los patrones de variante se multiplicaron, el uso de color divergió sutilmente, los estados de accesibilidad quedaron inconsistentes, y **el costo de corrección superó las ganancias de productividad iniciales**. Es el "antes" concreto que justifica el control layer.

_(Caso propio pendiente: color-ramp es founder-solo, no requirió buy-in interno.)_

## Gotchas / edge cases

- **Pitch como "design upgrade"** → pierde contra feature roadmap. SIEMPRE encuadrarlo como risk management.
- **SAFE como vanity scorecard** → si todos los rows leen "Medium", no es accionable. Forzar Low/High calls.
- **Capability ignorada** → invertir en infra sin training = scaled errors. Capability es parte del pitch.
- **Métricas aspiracionales** → si no tienes tus propios datos, las citas de fuentes son directionales, NO predictivas. Honesty matters; leadership detecta hand-waving.

## Checklist replicable

1. [ ] Step 0 SAFE diagnosis hecho (provee el scorecard concreto)
2. [ ] Identificar el riesgo organizacional concreto del dimensión más débil
3. [ ] Estimar cost-of-inaction (incidents, churn, accessibility, compliance)
4. [ ] Estimar ROI inicial de mover la dimensión más débil 1 step
5. [ ] AI fluency baseline incluido (Step 0.2)
6. [ ] Training plan asociado a la inversión
7. [ ] Presentar como "risk management mechanism", no como "design upgrade"
8. [ ] Métricas: propias > directionales de fuentes (cita si usás fuentes)
9. [ ] Próximos pasos concretos: qué fase, qué deliverables, qué timeline

## Referencias

- Output volume → variability → risk: `sources/10-grace-han-how-to-get-buy-in-for-an-ai-ready-design-system.md:35`
- DS como risk management: `:40`
- SAFE framework: `:86-108`
- Capability part of case: `:113` (header §"Make capability part of the case")
- Risk if weak (per SAFE dim): `:100-108` (tabla)
- Reframing reduces resistance: `:147`
- Productivity gains directionales: `:65`
- AI multiplier: `sources/11-grace-han-ai-ready.md:35`
- Dev velocity 3-4x: `sources/08-cristian-morales-part-8-what-building-agentic-systems-taught-me.md:22`
- Accuracy gains: `sources/02-cristian-morales-part-2-towards-an-agentic-design-system.md:42`
