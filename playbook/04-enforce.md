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
status: "v1 — reconciliado contra sources/06 completo (2026-07-23): +severidad, +taxonomía de intent, +composite/override, +epistémico, +skills del repo. Gotchas aún de color-ramp"
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

### El punto epistémico (lo más difícil no es técnico)

> "The harder part of governance isn't technical. It's epistemic. You have to articulate rules you've been following unconsciously." — `sources/06-...:204`

Codificar el auditor **obligó a Cristian a escribir qué significa "correcto"** — no como documentación, sino como conocimiento que el sistema puede ejecutar. Cada regla codificada era algo que ya sabía; el trabajo fue hacerlo legible para algo distinto de él mismo (`:204`). **Esto es lo que todo este playbook intenta hacer**, y por qué escribir el SPEC cuesta tanto: no es programar, es articular lo que sabías sin saber que lo sabías.

### El audit no produce calidad — verifica que el entorno la produce

El reencuadre clave del artículo. El componente `FilterBar` dio **cero violaciones**, no porque el auditor lo limpiara, sino porque se construyó *después* de que la infraestructura existiera:

> "The auditor didn't make FilterBar clean. The environment did. The auditor is how we verify that the environment is working." — `sources/06-...:168`

> "The goal isn't to catch violations. It's to make violations the harder path." — `sources/06-...:170`

Implicación para el diseño del CI: un componente limpio es **evidencia de que la infraestructura funciona**, no el resultado de que el CI lo corrigiera. El CI verifica el entorno; el entorno (scaffolding + convenciones + tokens consultables) es lo que produce la calidad.

### Governance ≠ Orchestration

Cristian separa dos problemas que se confunden (`:200-202`):

- **Orchestration** (parte 5, `sources/05-...`) — *capability*: lograr que la IA participe en el pipeline.
- **Governance** (esta fase) — *trust*: que puedas confiar en que lo construido es lo que diseñaste, sin verificar cada decisión a mano.

Orchestration mete la IA dentro del sistema; governance mantiene el sistema coherente al crecer. **Governance es la capa que viene *después* de tener orchestration funcionando** — no se pueden hacer en paralelo desde cero.

### Self-healing, no autónomo

> "I still decide which rules to encode, which gaps to fill, which patterns mean something. But the execution doesn't need me. The system closes the loop." — `sources/06-...:210`

El humano decide **qué** se codifica; el sistema ejecuta. Es la misma frontera que fija **ADR-022 §D6**: la decisión es humana, la ejecución se automatiza. Confundirlas —dejar que el sistema *decida* qué es correcto— es donde vuelve la alucinación.

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

### C.3 — Modelo de severidad (de Cristian Part 6)

Toda violación no pesa igual. Cristian clasifica en 3 niveles (`sources/06-...:172-178`), **brand-agnostic y directamente replicable**:

| nivel | categorías | impacto | acción |
|---|---|---|---|
| **Critical** | colores hardcodeados · tokens fantasma (referencia a un token que no existe) | rompe el theming, **falla en silencio en runtime** | must fix |
| **Warning** | tipografía primitiva (sugerir composite) · par tipográfico mal emparejado · jerarquía de foreground mal usada · color primitivo crudo · spacing/radius/shadow/z-index hardcodeados · valores off-scale | inconsistente con las decisiones de diseño | should fix |
| **Info** | coherencia de elevación · intención de capa de fondo · motion hardcodeado · notas de área sin decidir | mismatch menor o área futura | nice to fix |

El criterio que separa Critical de Warning: **¿falla en silencio, o solo es inconsistente?** Un token fantasma compila y no pinta nada (Critical); un `--foreground-muted` mal usado se ve, solo que en la posición equivocada (Warning).

### C.4 — Cómo se ve una violación de *intención* (no de sintaxis)

El nivel 2 (governance) es abstracto hasta que se ve un ejemplo. Estas son violaciones donde **cada token es válido pero su relación rompe la regla** (`sources/06-...:182-192`):

| violación | severidad | qué marcar |
|---|---|---|
| Tipografía primitiva en vez de composite | Warning | `font-size` + `font-weight` + `line-height` sueltos en vez de un token `--text-*`. Sugerir el composite. |
| Par tipográfico desemparejado | Warning | cuando se usan primitivos, el par `font-size ↔ line-height` no coincide con ningún composite |
| Jerarquía de foreground mal usada | Warning | `--foreground-muted` para texto de contenido (es para *placeholders*); `--foreground-subtle` para placeholders (es para texto secundario) |
| Color primitivo crudo | Warning | referencia directa a `--color-{palette}-{shade}` — salta la capa semántica |
| Elevación vs tamaño | Info | nivel de elevación desproporcionado al tamaño del elemento |
| Off-scale spacing | Warning | valor hardcodeado que no coincide con ningún paso de la escala |

> "Using `--foreground-muted` for body copy isn't a missing token — it's the wrong position in the hierarchy." — `sources/06-...:118`

### C.5 — La regla composite-sobre-primitivo, y su matiz humano

Patrón de regla generalizable (`sources/06-...:100-106`): **usar siempre el token compuesto** (`--text-body-md-regular`) que fija tamaño + peso + interlineado a la vez, no los tres primitivos por separado.

**El matiz que importa, y que conecta con **ADR-022 §D6**:** un override a primitivos *"is a human design decision"* (`:106`). Por eso se marca **Warning, no Critical** — el auditor reconoce que el override *puede* ser intencional. No toda desviación es un error: algunas son decisiones humanas legítimas, y el sistema tiene que distinguir las dos. Un CI que trate todo override como error entrena a la gente a desactivarlo.

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

### D.4 — Los 3 Must de la fase (Grace Han, Phase 4)

Grace lista tres Must para "make it real" (`sources/11-...:189-193`). Dos están cubiertos; uno
se caía:

1. **Lint contra el uso de tokens** (`sources/11-...:191`) — el `token-audit` de C.3/D.1.
2. **Bloquear valores fuera del sistema en los PR** (`sources/11-...:192`) — el CI gate de D.1.
3. **Rastrear la adopción de componentes** (`sources/11-...:193`) — **el que faltaba.** El
   capítulo mide *violaciones y drift*, no *adopción*: qué porcentaje de la app consume el
   sistema. Son métricas distintas (es la lección F1: adherencia ≠ cobertura). Sin tracking de
   adopción no sabes si el sistema se usa; con él, priorizas migraciones con datos. Es la
   entrada del usage-tracker de la fase 14 (Gobernar).

Sus tres Should —version releases, changelogs, deprecation policies (`sources/11-...:197-199`)—
se tocan vía Quick win #3 y el crystallization protocol, pero no como práctica explícita de la
fase.

## Las skills que lo implementan (ya en el repo)

El artículo nombra dos skills que Cristian construyó (`sources/06-...:93,95`), y **este repo tiene las dos**:

- **`framework/skills/scaffold-component`** — genera la estructura de archivos, interfaces y CSS modules con referencias a token. Es lo que hace que "cumplir sea el camino de menor resistencia".
- **`framework/skills/component-audit`** — el equivalente al `token-auditor`: escanea, compara cada `var(--*)` contra el schema, marca lo que no está.

El scaffold es la mitad preventiva (facilita lo correcto); el audit es la mitad verificadora (confirma que el entorno funciona). Las dos juntas son el "environment design" del nivel 3.

## Ejemplo concreto — Enara (el sistema de Cristian)

Los números del artículo, que son la evidencia de que la progresión v1→v2 funciona (`sources/06-...:120,146-151`):

- Auditor **v1** (existence) sobre la capa de moléculas: **43 violaciones**.
- Auditor **v2** (intent) sobre los mismos archivos: **64 violaciones — +21 semánticas** que v1 no veía.
- Desglose v2: 22 Critical · 30 Warning · 12 Info.
- Las +21 nuevas: colores primitivos usados directo (`--color-green-500`), un `--color-blue-500` que **no existe como palette**, tokens fantasma, y mismatches tipográficos (`:153-159`).

> "That number could look like regression. It isn't. The auditor got smarter." — `sources/06-...:162`

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
