---
phase: "Fase A — Capa semántica"
audience: "Designer + dev de plataforma que van a explicitar la cadena Tier 3 → Tier 2 → Tier 1"
prerequisites: "Step 0 completado (Stabilise). SSOT identificado."
primary_intent: constraint
secondary_intents: [guideline, workflow]
sources:
  - sources/09-diana-wolosin-intent-driven-context-for-ai-design-systems.md (intent classification)
  - sources/11-grace-han-ai-ready.md (Phase 2 Structure)
  - sources/03-cristian-morales-part-3-design-system-documentation-as-structured-metadata.md (token schema shape)
  - sources/04-cristian-morales-part-4-mapping-your-design-system-for-ai-agents.md (relationship map)
status: "Draft — contenido escrito; Gotchas a llenar con aplicación real fuera de color-ramp"
---

# 02 — Structure: capa semántica explícita + intent classification

## Cita verbatim

> "Context is not documentation. Context is intent."
> — `sources/09-diana-wolosin-intent-driven-context-for-ai-design-systems.md:158-159`

> "Without a map, AI explores. With a map, AI navigates. The difference is determinism."
> — `sources/04-cristian-morales-part-4-mapping-your-design-system-for-ai-agents.md:18`

## [INTERPRETACIÓN]

Fase A tiene **dos sub-fases que NO son negociables en orden**:

1. **Intent classification primero** (Diana) — antes de modelar schema, clasificar qué conocimiento existe y cómo se entrega.
2. **Schema + relationship map después** (Cristian) — con la clasificación clara, los schemas tienen forma deterministic.

Saltar 1 e ir a 2 produce schemas que mezclan intents (un blob "documentation") y delivery mechanisms (un MD que pretende ser constraints + guidelines + framing + workflows simultáneamente). Diana lo llama "ineffective and interchangeable documentation" (`sources/09-...:74`).

## Patrón replicable

### Step A.0 — Intent classification

Inventario de TODO el conocimiento del DS (prose docs, schemas existentes, decisiones tácitas) y clasificación en una de las 4 categorías Diana:

| Categoría | Propósito | Delivery mechanism | Ejemplos |
|---|---|---|---|
| **Constraints** | Behavior guardrails — qué *must* o *must not* pasar | AGENTS.md, linters, system prompts | "Tokens semánticos solo via SSOT, no inline" |
| **Guidelines** | Reference lookup — answer questions | MCPs, structured knowledge bases, APIs | "¿Qué hex resuelve `{role}-bg-default`?" |
| **Framing** | Shape decisions — explain *why* | AGENTS.md, system prompts | "Filosofía del DS: encoder decisions, not handoff hope" |
| **Workflows** | Guide execution — pasos secuenciales | Skills, task-specific prompts | "Cuándo invocar audit loop multi-experto" |

Deliverable: `intent-map.json` con shape:
```json
{
  "<knowledge-piece-id>": {
    "category": "constraints | guidelines | framing | workflows",
    "delivery": "<mechanism>",
    "source": "<file:line or doc reference>",
    "ownership": "<role/person>"
  }
}
```

### Step A.1 — Token schema (canónico, brand-agnostic)

Shape genérico (ver `framework/schemas/token.schema.json`):
```json
{
  "tier3": "{role}-{context}-{intensity}[-{interaction}]",
  "layer": "global | semantic | state",
  "references": {
    "semantic": "<tier-2 ref>",
    "primitive": "<tier-1 ref>"
  },
  "value": "<domain-specific raw value>",
  "wcag": {
    "level": "A | AA | AAA",
    "threshold": "<numeric>",
    "measurement": "<measured>",
    "method": "<cr | apca | other>"
  },
  "rules": ["<rule-id ref>"]
}
```

**Nota crítica**: el campo `wcag` es **primitive-level**, no afterthought Fase D. Ver **ADR-011**. Esta es la promoción del patrón color-ramp `tokens/*.json` con `cr/wcag/wcagThreshold` per token + §22 measurement-anchored rules.

### Step A.2 — Relationship map

Mapping component → uses → components/tokens. Cristian Part 4 (`sources/04-...:44`):
> "If you grep for `<Tooltip>` in my pages, you find nothing. Tooltip is three levels deep. But it's very much in use."

Shape mínimo (TOON-format-friendly):
```
ComponentX:
  uses:[ ComponentA, ComponentB ]
  tokens:[ {role}-bg-default, {role}-text-on-fill ]
  path: src/components/...
```

Auto-generable. Cristian Part 4 (`:235`): "The index is auto-generated. A Python script scans `src/components`, parses imports, builds the relationship graph, outputs TOON files."

## Ejemplo concreto (color-ramp)

Color-ramp implementa Tier 1 → 2 → 3 cascade en `palette-hsl.js` + `color-system.md`. Token shape ejemplo:
```json
{
  "tier3": "brand-bg-default-hover",
  "layer": "state",
  "references": { "semantic": "brand-bg-default", "primitive": "brand-1000" },
  "value": "#8a1cd1",
  "cr": 6.63,
  "wcag": { "level": "AAA", "threshold": 4.5 }
}
```

Pendiente formalizar como JSON Schema. Color-ramp tiene este shape en `tokens/*.json` pero falta el `schema.json` que lo valide via ajv.

Intent classification para color-ramp (parcial):
- Constraints → §2 rangos CR, §3 pivot rules, invariants I1-I6
- Guidelines → recipes.json + spec-index.json
- Framing → CLAUDE.md (3 categorías hue, multibrand mandate)
- Workflows → `.claude/skills/design-system-audit-loop.md`

## Gotchas / edge cases

- _A llenar conforme se ejecuta intent classification fuera de color-ramp_
- Asumir que un MD es "documentation" único es el anti-pattern Diana identifica: probable que el MD esté mezclando 3 de los 4 intents

## Quick wins paralelos asociados (ver [ROADMAP §Quick wins](../AI-READY-ROADMAP.md#-quick-wins-paralelos))

- **Quick win #2**: Schema validation con `ajv` o equivalente — detecta null/undefined en generate, no en audit. Aplica a `token.schema.json` que produce este chapter.
- **Quick win #4**: `examples/` folder con código de uso (CSS, React, Swift, etc.) — validación cross-platform del shape generado.

## Checklist replicable

1. [ ] Inventariar TODO el conocimiento existente del DS (prose, schemas, decisiones tácitas)
2. [ ] Clasificar cada pieza en una de las 4 categorías Diana
3. [ ] Producir `intent-map.json` con clasificación + delivery + source + owner
4. [ ] Diseñar shape del token canónico (Tier 3 → 2 → 1 cadena explícita)
5. [ ] Reservar campos a11y como primitive (`wcag.level`, `threshold`, `measurement`, `method`)
6. [ ] Generar relationship map auto desde el codebase
7. [ ] Validar: un LLM puede resolver "¿qué resuelve `{tier3-name}`?" sin inferir

## Referencias

- Intent categories table: `sources/09-diana-wolosin-intent-driven-context-for-ai-design-systems.md:69-72`
- Delivery mechanisms table: `sources/09-diana-wolosin-intent-driven-context-for-ai-design-systems.md:165-175`
- Token schema as structured metadata: `sources/03-cristian-morales-part-3-design-system-documentation-as-structured-metadata.md` (full file)
- Relationship maps: `sources/04-cristian-morales-part-4-mapping-your-design-system-for-ai-agents.md:18, :44, :235`
- A11y as primitive ADR: **`../docs/adr/ADR-011-a11y-as-primitive.md`**
