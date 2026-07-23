---
phase: "Introduction"
primary_intent: framing
audience: "Cualquiera que llega a este repo por primera vez (designer, dev, tech lead)"
prerequisites: "Ninguno — este es el punto de entrada"
status: "Draft — intro escrita; estructura del playbook completa (9 caps)"
---

# 00 — Introducción al playbook

## Qué es esto

Este playbook es una **metodología replicable** para construir un agentic design system desde cero. No es un design system específico, no es un componente library, no es un plugin. Es la **secuencia de decisiones, diagnósticos y artefactos** que un equipo cualquiera puede seguir para que su DS sea AI-ready (consumible y gobernable por agentes IA).

El framework subyacente combina:
- 4 fases de Grace Han ("How to make your design system AI-ready")
- Intent-driven context de Diana Wolosin
- Estructuras de Cristian Morales (8 partes: agentic DS desde cero, RAG, structured metadata, mapping, orchestration, governance, capa humana, lecciones)

Síntesis de los 11 artículos en **`../sources/SYNTHESIS.md`** (8 temas + 1 contradicción + 4 gaps + 3+1 ajustes al roadmap).

## A quién va dirigido

- **Designer + tech lead** de un equipo con un DS existente que quiere acelerar con AI sin scaled errors
- **DS architect** empezando un sistema nuevo que debe ser AI-ready from day one
- **Engineering lead** evaluando si invertir en infra AI-ready y cómo presentar el caso a leadership

NO va dirigido a: leer cold para entender qué es AI en general. Asume conocimiento básico de design systems + interés operativo en agentes IA.

## Cómo leer

**Orden recomendado**:
1. Este archivo (`00-introduction.md`) — context + glossary + secuencia
2. [`../AI-READY-ROADMAP.md`](../AI-READY-ROADMAP.md) — el plan framework-level (brand-agnostic)
3. **`../sources/SYNTHESIS.md`** — base teórica con citas verbatim (saltar si no quieres profundidad)
4. Los chapters siguientes en orden de fase, NO todos a la vez
5. **`../case-studies/color-ramp/ROADMAP.md`** si quieres ver aplicación concreta a un dominio (color)

**Si tienes 30 min**: lee este archivo + [`../AI-READY-ROADMAP.md`](../AI-READY-ROADMAP.md). Suficiente para saber si el playbook aplica a tu equipo.

**Si tienes 2 horas**: agregá **`../sources/SYNTHESIS.md`** + el chapter de la fase donde estás (Step 0 si arrancás).

## Glosario

### Framework-level (brand/dominio-agnostic)

| Término | Definición | Fuente |
|---|---|---|
| **SSOT** | Single Source of Truth — el archivo canónico que define un dominio. NUNCA duplicar. | Convención industria |
| **Tier 1 / 2 / 3** | Capas de tokens: 1 = primitive raw (`{primitive}-{N}`), 2 = semantic alias (`{role}-{N}`), 3 = contextual (`{role}-{context}-{intensity}[-{interaction}]`). Solo Tier 3 se consume en UI. Aliases industria: primitive / semantic / contextual. | Grace Han `sources/11-...:121-125` |
| **Intent category** | 4 buckets de Diana Wolosin para clasificar knowledge: Constraints, Guidelines, Framing, Workflows. Cada uno tiene su delivery mechanism. | `sources/09-...:69-72` |
| **SAFE** | Framework Grace Han 4-dim: Structure / Alignment / Formalisation / Enforcement. Tool para diagnosticar madurez. | `sources/10-...:86-108` |
| **ARC** | Progresión Cristian Part 2: Audit → Report → Compose. Trayectoria evolutiva de AI consumption de un DS. | `sources/02-...:263-279` |
| **WCAG / CR / AA / AAA** | Web Content Accessibility Guidelines. CR = Contrast Ratio. AA = 4.5:1 normal text. AAA = 7:1. | W3C WCAG 2.2 |
| **APCA** | Accessible Perceptual Contrast Algorithm. Alternativa moderna a WCAG CR. | W3C draft |
| **MCP** | Model Context Protocol. Spec abierta para conectar AI agents a structured knowledge sources. | Anthropic 2024 |
| **ADR** | Architectural Decision Record. Doc breve: contexto / opciones / decisión / consecuencias. Convención Michael Nygard. | Convención industria |
| **AI fluency (4Ds)** | Anthropic AI Fluency Index: Delegation, Description, Discernment, Diligence. | `sources/08-...:56` |
| **Pool A / Pool B** | Audit-loop skill: Pool A = 6 core experts del dominio; Pool B = lenses adversariales para rotación anti-bias. ≥2 Pool B por loop ≥2. | color-ramp `.claude/skills/design-system-audit-loop.md` |
| **Dual audit** | Pattern: standard rigor + creative adversarial en pasadas secuenciales (no parallel) sin compartir context, para detectar blind spots del audit suite. | Loop 7 color-ramp 2026-05-11 |

### Case-study-specific (color-ramp domain)

Estos términos viven en el case study, NO en el framework. Documentados aquí para que un lector que llega vía un chapter pueda decodificar referencias.

| Término | Definición | Fuente |
|---|---|---|
| **Pivot (color)** | Step donde la palette alcanza CR ~4.5:1 vs blanco. Color-ramp: 900 normal, 500 yellow. | color-ramp `color-system.md §3.1` |
| **Recipe block** | Bloque ` ```recipe ` embebido en MD, machine-readable, parseable a JSON. Origen color-ramp; **grammar generalizable** (candidato en `framework/CANON.md`). | color-ramp `color-system.md §10+` |
| **Slot / slot engine** | Pattern color-ramp: cada elemento del UI declara `data-slot-spec="component.variant.state"`, runtime resuelve token. **Candidato a abstracción framework**. | color-ramp `magic-pro-editor.html` |
| **specHash** | Hash sha256-12 del MD spec, embedded en derived artifacts. Drift detection cross-files. **Pattern generalizable**, ver `framework/CANON.md` candidato. | color-ramp `scripts/parse-spec.mjs` |

## Secuencia operacional replicable

Derivada de SYNTHESIS §9. Cada step mapea a un capítulo del playbook:

| Step | Fase roadmap | Capítulo | Aporte principal |
|------|--------------|----------|------------------|
| 0 | Pre-fase | [`01-stabilise.md`](01-stabilise.md) | SAFE diagnosis + AI fluency baseline + Accountability map |
| 1 | Fase A | [`02-structure.md`](02-structure.md) | Intent classification → token schema → relationship map |
| 2 | Fase B | [`03-formalise.md`](03-formalise.md) | Component manifests + Skills/Rules/Instructions trichotomy |
| 3 | Fase C | [`04-enforce.md`](04-enforce.md) (continuación de la apertura en `03-formalise.md`) | Rules registry + v1→v2 audit |
| 4 | Fase D | [`04-enforce.md`](04-enforce.md) | Enforcement + mutation testing + CI |
| 5 | Fase E (cross-cutting) | [`05-human-layer.md`](05-human-layer.md) | Accountability + AI fluency growth + source-to-execution traceability |
| 6 | Opcional | [`99-buy-in.md`](99-buy-in.md) | SAFE para leadership pitch |

Quick wins paralelos (no secuenciales) — mapping en [`../AI-READY-ROADMAP.md`](../AI-READY-ROADMAP.md) §"Quick wins".

**Capítulos de práctica** (deep-dives concretos, no lineales — se leen junto al step de su fase):
- [`06-token-grammar-and-audit.md`](06-token-grammar-and-audit.md) — práctica a nivel **token** (axes, patrones, audit loop). Acompaña Fase A/B.
- [`07-component-contracts.md`](07-component-contracts.md) — práctica a nivel **componente**: el método probado de contratación (inspect→audit→contrato→parity→ADR) + los 3 patrones de composición. Es el par concreto del `03-formalise.md`. Validado con el case study Link FieldIQ.

## Principios de escritura del playbook

Cada chapter debe tener:

1. **Cita verbatim** de la(s) fuente(s) principal(es) — con `sources/{file}.md:{line}`
2. **`[INTERPRETACIÓN]`** — síntesis del por qué (explícitamente marcada)
3. **Patrón replicable** brand-agnostic
4. **Ejemplo concreto** (referencia al case study color-ramp)
5. **Gotchas / edge cases** aprendidos al implementar
6. **Checklist replicable** para otro equipo

## No incluir en el playbook

- Código específico de un brand (eso vive en `case-studies/`)
- Decisiones que aplican solo a color (color-ramp tiene su propio repo)
- Recetas de Figma plugin (eso vive en color-ramp repo)

## Estado actual (snapshot)

Para estado vivo: ver [`../README.md`](../README.md) §Estado, **`../case-studies/color-ramp/ROADMAP.md`**, y `case-studies/<system>/STATE.md` per case study.

Este playbook está en **early stub** stage. La estructura está definida; los stubs de cada chapter tienen secciones + algunos contenidos. Pendientes:
- Ejemplos concretos en cada chapter (a llenar cuando se ejecute la fase fuera de color-ramp)
- Validación con un **segundo case study** (typography, motion, audio) — multibrand mandate (**ADR-013**)
- Materialización de primer schema concreto en `framework/schemas/` (todavía solo READMEs)

ADRs vivos (serie de proceso, reconciliada a `docs/adr/` 2026-06-13): **`ADR-010`** (split), **`011`** (a11y), **`012`** (trichotomy), **`013`** (canon gate), **`014`** (incident-crystallization pattern).
