---
phase: "Fase C (rules-as-data) + Fase D (enforcement)"
audience: "Tech lead + DS architect implementando audit infrastructure + CI"
prerequisites: "Fase A (token schema) + Fase B (component manifest) completadas"
primary_intent: constraint
secondary_intents: [workflow]
sources:
  - sources/06-cristian-morales-part-6-encoding-governance-on-agentic-design-systems.md (v1→v2, governance)
  - sources/11-grace-han-ai-ready.md (Phase 4 Enforce)
  - sources/02-cristian-morales-part-2-towards-an-agentic-design-system.md (ARC protocol)
status: "Draft — contenido escrito; Gotchas a llenar con aplicación real fuera de color-ramp"
---

# 04 — Enforce: rules-as-data + governance

## Cita verbatim

> "v1 checked existence. v2 checks intent. The difference between those two things is the difference between a linter and governance."
> — `sources/06-cristian-morales-part-6-encoding-governance-on-agentic-design-systems.md:163-165`

> "The goal isn't to catch violations. It's to make violations the harder path."
> — `sources/06-cristian-morales-part-6-encoding-governance-on-agentic-design-systems.md:169`

> "You're not designing the interface. You're designing the conditions under which interfaces get built correctly."
> — `sources/06-cristian-morales-part-6-encoding-governance-on-agentic-design-systems.md:205`

## [INTERPRETACIÓN]

Enforcement maduro tiene **3 niveles progresivos**:

1. **Linter v1 (existence)**: ¿el token existe en el schema?
2. **Governance v2 (intent)**: ¿el token cumple la *relación* con otros tokens que la regla prescribe?
3. **Environment design**: el path correcto es el más fácil; el incorrecto requiere effort consciente.

La progresión es la diferencia entre **catching mistakes** y **preventing them by design**. Fase C codifica las reglas como data; Fase D las ejecuta en CI con criterios "violations impossible by-design".

## Patrón replicable

### C.1 — Rules registry shape

Cada regla atómica:
```json
{
  "id": "<unique-rule-id>",
  "section": "<spec section reference>",
  "title": "<human-readable short description>",
  "predicate": "<machine-readable AST>",
  "expected": { "<key>": "<value or ref>" },
  "wcag": { "level": "AA", "threshold": 4.5 },
  "appliesTo": ["<component>.<variant>", "..."]
}
```

Predicates grammar (generalizable, brand-agnostic):
- Atoms: `<axis>:<value>` (ej `category:warm`, `state:hover`, `mode:dark`)
- Negation: `!<atom>`
- Conjunction: `<atom>+<atom>`
- Disjunction: documentado como múltiples rules con misma `id` prefix

Refs grammar:
- Direct: `<token-id>` (Tier 2 o 3)
- Relative: `pivot[+/-N]`, `self-N` (positional)
- Special: `transparent`, `white`, `<role>-N`, `alpha:<expr>@N`

### C.2 — Cross-rule validation

Audit que detecta:
- **A — cross-conflict**: dos reglas distintas prescriben distinto `expected` para mismo `predicate` con `appliesTo` overlap
- **B — stale ref**: regla referencia `appliesTo` componente/variant que no existe
- **C — stale section**: regla referencia spec section que ya no existe
- **D — predicate vocab**: regla usa predicate no documentado en grammar
- **E — empty rule**: regla sin `expected` o sin `appliesTo`

Este audit cierra el gap "spec se valida contra código pero no contra sí misma" — exactamente lo que Cristian Part 6 describe.

### D.1 — Audit suite + CI

Stack mínimo:
- **Static audit** (`scripts/audit.mjs` equivalent): corre offline, valida schemas + cross-rule + spec drift via hash
- **Mutation testing**: catálogo de regresiones conocidas; cada bug fixed agrega una mutation. El audit debe cazar todas las mutations catalogadas
- **Runtime invariants**: assertions que corren en producción al render time, catch lo que el static audit no puede (ej: progresiones de estado, contraste real DOM-computed)
- **CI gate**: GitHub Actions o equivalente. PR no merges si audit/mutate fallan.

### D.2 — Crystallization protocol

Cada bug fixed genera **al menos una** persistence:
- Invariant nuevo
- Audit check nuevo
- Spec section requirement nuevo
- Doc line nueva

Sin persistence, el bug volverá. Per Cristian Part 6: "make violations the harder path" = make each fixed violation impossible to silently re-introduce.

### D.3 — ARC progression

Per Cristian Part 2 (`sources/02-...:263-279`):
- **Audit (consumer)**: AI lee el DS con accuracy 100%
- **Report (maintainer)**: AI identifica technical debt, sugiere refactors
- **Compose (self-governing)**: AI detecta drift, genera fixes, mantiene sin intervention

A → B → C captura la trayectoria evolutiva. Fase D mature = ARC Phase 2 (Report). Fase D + Fase C mature = camino hacia ARC Phase 3 (Compose).

## Ejemplo concreto (color-ramp)

Color-ramp implementa esto al detalle. Estado al 2026-05-12:
- **Static audit**: 10 checks (`npm run audit`)
- **Mutation testing**: 14 mutations detected (`npm run mutate`)
- **Runtime invariants**: 6 (I1-I6 en `magic-pro-editor.html` `runInvariantChecks()`)
- **Browser audits**: 9 más vía `runAllChecks()`
- **CI**: `.github/workflows/audit.yml` corre audit + mutate + bench
- **Rules registry**: 286 atomic rules + 3 constraints derivados de 22 recipes via `parse-rules.mjs`
- **Cross-rule audit**: `rulesRegistryLint` con 5 sub-checks (A-E listados arriba)

Patron `componentMetadataLint` + `_meta.specHash` propaga drift detection: cuando spec cambia, todos los 9 componentes detectan stale hash en lockstep.

Ver `color-ramp/DEVELOPER-HANDOVER.md §4.4` para el audit suite completo, `color-ramp/scripts/audit.mjs` para implementación.

> ⚠ Cross-repo: estos archivos viven en `~/Documents/Zoom/color-ramp/` (repo privado). Si no tienes acceso, **`case-studies/color-ramp/ROADMAP.md`** §"Fase D aplicada" describe el shape sin requerir el código.

## Gotchas / edge cases

- **Audit suite es self-bias**: quien escribe el audit conoce los failure modes que está cazando. Necesita **mutation testing + creative adversarial passes** (color-ramp Loop 7 dual mode) para detectar blind spots.
- **Runtime invariants vs static lints**: runtime invariants cazan lo que solo se manifiesta en composición real (ej: contraste DOM-computed). Static lints cazan lo declarativo. No son intercambiables.
- **`specHash` cross-cuts**: si tu hash live en N archivos, los N necesitan bumping coordinado en cada cambio. Audit debe detectar drift (color-ramp `componentMetadataLint`).
- **CI sin mutation testing degrada**: si CI solo corre static audit, el audit gana confianza falsa. Mutation valida que el audit sigue efectivo cuando el codebase evoluciona.

## Checklist replicable

1. [ ] Extraer reglas del SSOT (MD/spec) a `rules/registry.json` con grammar generalizable
2. [ ] Implementar parser MD → rules registry (idempotente, regenerable)
3. [ ] Cada token apunta a su(s) `rules: [...]`
4. [ ] Static audit en `scripts/audit.mjs` que valida: helpers usage, slot coverage, required specs, MD drift, forbidden literals, kind validation, component metadata lint, cross-rule lint
5. [ ] Cross-rule lint con sub-checks A-E (conflict, stale ref, stale section, vocab, empty)
6. [ ] Mutation testing catalog con ≥10 regresiones conocidas
7. [ ] Runtime invariants para lo que solo se valida en composición
8. [ ] CI pipeline (GitHub Actions) corre audit + mutate en cada PR
9. [ ] Convención de crystallization: cada bug fixed genera invariant/audit/doc
10. [ ] Criterio de éxito: violations imposibles by-design, no "violations caught"

## Quick wins paralelos asociados (ver [ROADMAP §Quick wins](../AI-READY-ROADMAP.md#-quick-wins-paralelos))

- **Quick win #1**: Trazabilidad hardcoded (`rules: ["<rule-id>"]` por token) — trazabilidad sin refactor estructural. Pre-rules-registry-completo.
- **Quick win #3**: Version header en SSOT (`@version X.Y.Z-rc.N`) — pre-semver formal. Es el primer paso hacia semver auto + CHANGELOG.md gen.

## Referencias

- v1 vs v2 governance: `sources/06-cristian-morales-part-6-encoding-governance-on-agentic-design-systems.md:163-165`
- Make violations harder path: `sources/06-...:169`
- Environment design: `sources/06-...:205`
- Silent failure incident (`var(--color-blue-500)`): `sources/06-...:22-26`
- ARC protocol: `sources/02-cristian-morales-part-2-towards-an-agentic-design-system.md:263-279`
- Audit history (case study): `~/Documents/Zoom/color-ramp/.claude/audit-history.json`
