---
phase: "Fase B — Components as APIs + apertura Fase C"
audience: "Designer + dev creando contratos formales para componentes"
prerequisites: "Fase A completada (token schema + intent classification)"
primary_intent: guideline
secondary_intents: [workflow]
sources:
  - sources/11-grace-han-ai-ready.md (Phase 3 Formalise)
  - sources/03-cristian-morales-part-3-design-system-documentation-as-structured-metadata.md (component metadata shape — el más denso)
  - sources/05-cristian-morales-part-5-agent-orchestration-for-design-systems.md (Skills/Rules/Instructions trichotomy)
status: "Draft — contenido escrito; Gotchas a llenar con aplicación real fuera de color-ramp"
---

# 03 — Formalise: components as APIs

## Cita verbatim

> "Many systems contain well-designed components that are structurally vague. Flexibility without a contract leads to interpretation."
> — `sources/11-grace-han-ai-ready.md:152-154`

> "A component that behaves like an API is safer to automate than one that behaves like a flexible canvas."
> — `sources/11-grace-han-ai-ready.md:173`

> "It's not about creating new documentation. It's about reformatting existing knowledge."
> — `sources/03-cristian-morales-part-3-design-system-documentation-as-structured-metadata.md:558`

## [INTERPRETACIÓN]

Un componente como **API** vs como **picture**:
- Picture: estados visuales con tokens, useable por humanos que mantienen el contexto en su cabeza.
- API: axes declaradas (`variant × role × state`), slots tipados, constraints explícitos, antiPatterns documentados, aiHints para que un LLM elija correctamente.

La diferencia no es de tooling — es de **encoding decisions** vs **handing them off**. Sin contratos formales, AI infiere; con contratos, AI deterministicamente selecciona.

## Patrón replicable

### B.1 — Component manifest

Schema Cristian Morales Part-3 (shape completo en `sources/03-...:178` ButtonMetadata + 5 campos críticos para AI listados en `:330-336`), brand-agnostic:

```json
{
  "_meta": {
    "schemaVersion": "1.0",
    "version": "<component-version>",
    "generated": "<ISO-date>",
    "specHash": "<hash for drift detection>",
    "source": "<file path of source-of-truth>",
    "rationale": "<short reason this component exists>"
  },
  "component": {
    "name": "<ComponentName>",
    "category": "atom | molecule | organism",
    "description": "<one-line>",
    "type": "<interactive | display | layout>",
    "path": "<location in codebase>"
  },
  "variants": {
    "<axis-1>": ["<value>", "..."],
    "<axis-2>": ["..."],
    "state": ["default", "hover", "active", "focus", "disabled"]
  },
  "slots": {
    "<slot-name>": { "kind": "<bg|text|border|icon|...>", "ref": "<token reference>" }
  },
  "behavior": {
    "states": "...",
    "interactions": "...",
    "stateOverrides": "..."
  },
  "accessibility": {
    "role": "<ARIA role>",
    "keyboardSupport": "...",
    "wcag": { "level": "AA", "criteria": ["..."] }
  },
  "aiHints": {
    "priority": "<primary | secondary | tertiary>",
    "keywords": ["..."],
    "selectionCriteria": "<when to use this component>",
    "decisionTree": "..."
  },
  "usage": {
    "useCases": ["..."],
    "commonPatterns": ["..."],
    "antiPatterns": ["<scenario>: <why wrong>: <alternative>"]
  },
  "examples": {
    "minimal": "<code>",
    "fullVariantMatrix": "..."
  },
  "rules": ["<rule-id refs>"],
  "audit": {
    "slotCoverage": "<N>",
    "totalStateCombinations": "<N>"
  }
}
```

### B.2 — Skills/Rules/Instructions trichotomy

Decisión arquitectural per Cristian Part 5 (`sources/05-...:34-58`): la separación entre Skills (executable), Rules (passive context), Instructions (strategy/methodology) NO es opcional. Ver **ADR-012**.

Cada `framework/skills/`, `framework/rules/`, `framework/instructions/` tiene su README explicando intent category + delivery mechanism.

### B.3 — Apertura Fase C (rules como data)

Fase B documenta componentes individualmente. Fase C extrae las **reglas cross-componente** a un registry (siguiente capítulo, `04-enforce.md`). Apertura aquí:

- Identificar qué slots de qué componentes comparten constraint subyacente (ej: "todos los `bg-default` filled = pivot del role")
- Marcar candidatos para extraction a `rules/registry.json`

## Ejemplo concreto (color-ramp)

Color-ramp tiene 9 componentes documentados con Cristian Part-3 shape (`color-ramp/components/*.metadata.json`): Alert, Button, Card, Checkbox, Chip, Input, Radio, Tabs, Toggle. Cada uno valida via `componentMetadataLint` contra `spec-index.json` y `SLOT_DEFS`.

Patron `_meta.specHash` cross-cuts: cuando spec cambia, `componentMetadataLint` detecta drift via hash mismatch en todos los 9 componentes en lockstep.

## Gotchas / edge cases

- **Component schema sin `antiPatterns` colapsa** — Cristian Part 3 (`sources/03-...:574`): "You can't write 'don't overuse primary buttons.' You have to write: 'Multiple primary buttons in same section creates visual hierarchy confusion. Use one primary and secondary/ghost for other actions.'" Especificidad o nada.
- **Axis indexing vs flat naming**: `"Primary — Hover"` (flat string) es harder a parsear que `{variant: "primary", state: "hover"}` (axes). Reshape early.
- **Schema sin `aiHints` deja al LLM inferir**: cada componente debe tener `selectionCriteria` explícito + `keywords` para retrieval.

## Checklist replicable

1. [ ] Listar todos los componentes del DS
2. [ ] Para cada componente: identificar axes (variant, role, state, etc.)
3. [ ] Para cada axis: enumerar valores válidos (no "etc.")
4. [ ] Para cada slot: declarar `kind` + `ref` a token canónico
5. [ ] Para cada componente: escribir `antiPatterns` con (scenario, why, alternative) — especificidad obligatoria
6. [ ] Para cada componente: declarar `aiHints.selectionCriteria` + `keywords`
7. [ ] Validar component metadata contra schemas (lint)
8. [ ] Identificar reglas cross-componente → candidatos a Fase C registry
9. [ ] Confirmar trichotomy Skills/Rules/Instructions implementada en `framework/`

## Referencias

- Component metadata shape: `sources/03-cristian-morales-part-3-design-system-documentation-as-structured-metadata.md` (full file — el más denso técnicamente)
- ButtonMetadata schema export: `:178`
- "Critical for AI" 5 fields: `:330-336`
- "Reformatting existing knowledge" cita: `:558`
- Anti-patterns specificity: `:574`
- Trichotomy Skills/Rules/Instructions: `sources/05-cristian-morales-part-5-agent-orchestration-for-design-systems.md:34-58`
- ADR Trichotomy: **`../docs/adr/ADR-012-skills-rules-instructions-trichotomy.md`**
