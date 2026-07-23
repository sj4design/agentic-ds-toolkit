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
status: "v1 — reconciliado contra sources/03 y 05 completos (2026-07-23): +workflow de 5 pasos, +decisión de formato, +Header/Body, +4 estrategias de orquestación, +Prefer Editing Over Creating, +Must Phase 3 de Grace"
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
    "type": "<interactive | display | container | input>",
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

## Los 3 Must de la fase (Grace Han, Phase 3)

El capítulo cubre a fondo la formalización del contrato (schema de código, de Cristian Part 3),
pero Grace lista **tres** Must para "tratar los componentes como APIs" (`sources/11-...:168-172`)
y dos se caían:

1. **Definir variantes explícitas** (`sources/11-...:170`) — cubierto (axes + enumerar valores, sin "etc.").
2. **Eliminar lookalikes redundantes** (`sources/11-...:171`) — dos componentes que hacen casi lo mismo con nombres distintos. Es deuda que un agente amplifica: elige el equivocado con confianza.
3. **Alinear las variantes de Figma con los props de ingeniería** (`sources/11-...:172`) — el contrato de código y el de diseño deben decir lo mismo. Es la mitad *diseño* del contrato, que el schema-de-código por sí solo no cubre, y la que un DS multibrand necesita para que el brand-swap se pruebe también en Figma.

## La mitad operativa: de prosa a metadata (Cristian Part 3)

El schema de arriba es el **shape declarativo**. Pero este capítulo declara
`secondary_intents: [workflow]`, y la segunda mitad del artículo de Cristian es justamente ese
workflow: **cómo se genera la metadata a escala** sin escribirla a mano componente por componente.
El principio que lo gobierna:

> "It's not about creating new documentation. It's about reformatting existing knowledge."
> — `sources/03-...:558`

### Decisión de formato: JSON vs TypeScript vs Markdown

La metadata "can be JSON, Markdown, TypeScript, or whatever fits your tech stack"
(`sources/03-...:360`). Cada formato tiene su tradeoff:

| formato | a favor | en contra |
|---|---|---|
| **TypeScript** | snippets reales y ejecutables, type safety, puede importar los tipos del componente (`sources/03-...:362-367`) | atado al stack JS/TS |
| **JSON** | universal, cualquier tool lo parsea, validación simple | "Examples must be strings, not real code" (`sources/03-...:374`) |
| **Markdown** | legible en GitHub/Notion, fácil de revisar | "Harder to query programmatically" (`sources/03-...:380`) |

Cristian eligió TypeScript (`sources/03-...:383-387`). **Este playbook decide distinto** — JSON
canónico + TS por codegen (ADR-002) — y la divergencia es deliberada: el framework fija el *shape*,
el formato lo escoge cada equipo según su stack.

### Patrón Header / Body para discovery

El formato tiene dos partes con propósitos distintos (`sources/03-...:391`). El **header** (nombre,
categoría, descripción, tipo, ruta) se parsea primero y responde *"¿qué es esto, dónde está, de qué
categoría?"* antes de entrar en detalle (`sources/03-...:415`). El **body** lleva el intent y el uso.
La separación es de rendimiento: *"Tools can scan headers across all components to find candidates,
then read full metadata only for relevant components"* (`sources/03-...:431`). Escaneas headers
baratos sobre todo el DS y solo abres la metadata completa del puñado relevante.

### Workflow de 5 pasos: generar metadata a escala

1. **Audita dónde vive el conocimiento** (`sources/03-...:439`): Storybook y docs, descripciones y
   anotaciones de Figma, páginas de Notion/Confluence, **comentarios de code review** (los patrones
   que corriges una y otra vez) y conversaciones de Slack del tipo *"¿cuándo uso X en vez de Y?"*
   (`sources/03-...:441-445`). No creas conocimiento: inventarías el que ya existe (`sources/03-...:447`).
2. **Crea el template de metadata** con sus campos documentados en comentarios inline. "This
   template becomes your contract" (`sources/03-...:499`).
3. **Deja que la IA extraiga y popule**: apunta un agente a tu documentación con el template
   (`sources/03-...:503`). El artículo incluye el prompt literal (`sources/03-...:505-523`). "The AI
   reads prose documentation and outputs structured metadata. You review and refine"
   (`sources/03-...:525`).
4. **Scripts para lo mecánico** (`sources/03-...:529`): interfaces TypeScript → `props`; comentarios
   JSX → `description`; imports → `composition.nestedComponents`; git history → timestamps
   (`sources/03-...:531-534`).
5. **Itera** repartiendo por dificultad: extracción con IA donde hay docs ricos, traducción manual
   donde hay matiz, scripts para lo técnico en batch (`sources/03-...:542-544`).

> El reparto de labor es el punto, y es la frontera humano/máquina de este playbook:
> **"Scripts handle mechanical extraction. Humans add design intent."** — `sources/03-...:536`

### Los 4 workflows que la metadata habilita

Es la razón de todo el esfuerzo (`sources/03-...:548`): **agentes de selección de componente** (leen
`useCases`, `selectionCriteria` y `antiPatterns`), **grafos de relación** (categoría + composición
para contar instancias recursivamente), **herramientas de validación** (verifican el código generado
contra `antiPatterns` y `parentConstraints`) y **generación de código** (los `examples` como
plantillas, `props` como interfaz) — `sources/03-...:550-556`.

## Orquestación: las 4 estrategias y la regla anti-proliferación (Cristian Part 5)

La trichotomy Skills/Rules/Instructions es solo el preámbulo de Part 5. Su grueso operativo son
**cuatro estrategias** que un agente ejecuta sobre el índice, más una regla de composición que es la
de mayor valor accionable para formalizar componentes. Todo opera sobre el manifest: sin metadata
estructurada, ninguna tiene sobre qué correr.

1. **Deep tracing** — traversal recursivo hasta las hojas, con detección de ciclos
   (`sources/05-...:64-72`). Sirve para análisis de impacto de deprecación —deprecar un componente
   cascadea por tres niveles aunque "nada lo use directamente" (`sources/05-...:144`)— y para
   reportes de adopción certeros: sin él, los agentes reportaban 43-44 componentes de 57
   (`sources/05-...:146`).
2. **Instance counting** — imports dicen que *se usará*; las instancias, *cuántas veces*
   (`sources/05-...:150`). Edge cases que el algoritmo maneja: slots, render condicional y loops
   (`sources/05-...:169`). Detecta **shadow implementations**: componentes con imports cuyo trabajo
   hacen clases CSS por detrás, marcado como deuda técnica (`sources/05-...:192`).
3. **Hierarchy and structure** — los niveles atómicos como diagnóstico de salud al cruzarse con
   datos de instancia (`sources/05-...:209`). Y el matiz que evita malinterpretar: una adopción baja
   de átomos puede ser **filosofía de diseño**, no deuda —clases de utilidad en vez del componente
   `Spacer`—, y el agente debe reportarla como intencional (`sources/05-...:222`).
4. **Composition patterns** — que se materializa en la regla siguiente.

### La regla: Prefer Editing Over Creating

Antes de crear un componente nuevo: busca similares en el índice, mira si uno existente puede
extenderse con props o variantes, y compón los que ya hay (`sources/05-...:240-246`). **Crea uno
nuevo solo si** no existe similar, su responsabilidad es fundamentalmente distinta, o componer sería
demasiado complejo de mantener (`sources/05-...:248-254`).

El ejemplo canónico: no crear `PrimaryButton`/`SecondaryButton`, sino añadir
`variant?: 'primary' | 'secondary' | 'ghost' | 'danger'` a `Button` (`sources/05-...:256-266`). "Sin
esta regla, los agentes crean un componente nuevo por cada variación; con ella, extienden lo que
existe" (`sources/05-...:268`).

> **Es el mecanismo preventivo del Must 2 de Grace** (eliminar lookalikes): evita generarlos en
> primer lugar. Y explica la trichotomy: *"The rule provides context… The skill provides
> capability"* (`sources/05-...:290`) — regla = contexto, skill = capacidad.

### El feedback loop: los reportes refinan la infraestructura

El reporte de adopción no es una foto, es un mecanismo que mejora la propia infraestructura
(`sources/05-...:294`). Dos casos: la precisión del instance counting subió de **75% a 95%+** al
detectar componentes-slot y dejar de doble-contar (`sources/05-...:318,322-323`); y los timestamps
se movieron del filesystem a la metadata en TOON, porque en serverless los ficheros fuente no están
disponibles (`sources/05-...:329-338`). El efecto compuesto: cada reporte revela algo, cada insight
afila las instrucciones, el siguiente reporte es más preciso (`sources/05-...:344`). Eso es lo que
hace *agentic* a la infraestructura — **"The agent moves from consumer to maintainer"**
(`sources/05-...:348`).

> **Nota de secuencia (verificada):** Part 5 establece la *capacidad* de orquestación, prerrequisito
> de la *gobernanza* de Part 6 (capítulo 04). Ojo: Part 5 **no** menciona explícitamente por su
> nombre el tema "trust"/governance; el orden 05 → 06 se apoya en la numeración de la serie y en el
> arco consumer → maintainer con que cierra, no en una cita interna. Se deja dicho para no fabricar
> una referencia cruzada que la fuente no hace.

## Checklist replicable

1. [ ] (Grace P3) Eliminar lookalikes redundantes + alinear variantes Figma ↔ props de ingeniería
2. [ ] Listar todos los componentes del DS
3. [ ] Para cada componente: identificar axes (variant, role, state, etc.)
4. [ ] Para cada axis: enumerar valores válidos (no "etc.")
5. [ ] Para cada slot: declarar `kind` + `ref` a token canónico
6. [ ] Para cada componente: escribir `antiPatterns` con (scenario, why, alternative) — especificidad obligatoria
7. [ ] Para cada componente: declarar `aiHints.selectionCriteria` + `keywords`
8. [ ] Validar component metadata contra schemas (lint)
9. [ ] Identificar reglas cross-componente → candidatos a Fase C registry
10. [ ] Confirmar trichotomy Skills/Rules/Instructions implementada en `framework/`

## Referencias

- Component metadata shape: `sources/03-cristian-morales-part-3-design-system-documentation-as-structured-metadata.md` (full file — el más denso técnicamente)
- ButtonMetadata schema export: `:178`
- "Critical for AI" 5 fields: `:330-336`
- "Reformatting existing knowledge" cita: `:558`
- Anti-patterns specificity: `:574`
- Trichotomy Skills/Rules/Instructions: `sources/05-cristian-morales-part-5-agent-orchestration-for-design-systems.md:34-58`
- ADR Trichotomy: **`../docs/adr/ADR-012-skills-rules-instructions-trichotomy.md`**
