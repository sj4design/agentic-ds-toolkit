---
title: "Token grammar: el modelo de axes, los 8 patrones y el audit loop"
status: "v1 — escrito desde la implementación v0.4-0.5 del composer + ADR-009 + audit de 24 DS"
intent: "guideline"
audience: "steward que quiere hacer su DS legible para agentes sin reescribir su naming"
self_contained: true
---

# Capítulo 06 — Token grammar: axes, patrones y audit

> **Tesis del capítulo**: no necesitas adoptar UN naming "AI-ready". Necesitas que tu naming, **el que sea**, sea legible para un agente. La diferencia es un **modelo de axes** + un **audit que valida intent, no forma**.

Este capítulo documenta la metodología que emergió al hacer el composer gramática-agnóstico (**ADR-009**) y al auditar 24 design systems (**ai-readiness-audit**). Es replicable: no depende de color ni de un brand.

---

## 1 · El error que casi cometimos: format-first

La primera versión del clasificador asumía que un token Tier 3 sigue `{role}-{context}-{intensity}[-{interaction}]` (el patrón de color-ramp). Validaba **la forma del string**.

Eso es exactamente el anti-pattern que dos fuentes independientes rechazan:

> *"v1 checked existence. v2 checks intent."*
> — Cristian Morales, **`sources/06-...:163`**

> *"First Identify Intent → Then Classify Context"* … *"First Delivery Mechanisms → Then Format"* … *"Format is defined by delivery mechanisms."*
> — Diana Wolosin, **`sources/09-...:65,131,134`**

**[INTERPRETACIÓN]** Validar la forma del nombre pregunta "¿existe el patrón?" — la pregunta v1. La pregunta v2 es "¿qué intent declara este token?". Un sistema puede declarar el mismo intent (un color de fondo de marca en estado hover) con strings completamente distintos: `brand-bg-default-hover`, `primaryHover`, `interactive-01`, `--n-color-button-hover`. Forzar un formato excluye al 46% de los DS reales (**ADR-009 Context**) y contradice el mandato multibrand.

---

## 2 · El modelo de axes (parse → AST → serialize)

La solución (ADR-009, Option B) es dejar de operar sobre el string y operar sobre un **AST semántico canónico**:

```
namespace · role · context · intensity · state
```

Tres capas, una idea:

| Capa | Qué hace | Dónde vive |
|---|---|---|
| **Parser** | `string → AST de axes`. Normaliza formato (separador, casing) + etiqueta cada segmento contra el vocab. | `parseTokenToAST` |
| **Mecanismo** | Resuelve dónde vive el `context` cuando no es una palabra literal. | `detectMechanism` / `activeMechanism` |
| **Serializer** | `AST → string` en el patrón del sistema destino. | `serializeFromAST` / `serializeGeneratedName` |

**[INTERPRETACIÓN]** El naming pasa a ser una **proyección** del AST, no la fuente de verdad. Esto es el análogo a tokens de ADR-005 (composición como AST): modelas la estructura semántica, el string es entrada/salida. Una vez que tienes el AST, puedes auditar, generar y re-emitir en CUALQUIER patrón sin reescribir la lógica.

---

## 3 · Los 8 patrones y los 3 mecanismos

El audit identificó **8 patrones gramáticos canónicos** entre 24 DS. No necesitas 8 parsers: colapsan en **3 mecanismos** según *dónde vive el context*:

| Mecanismo | Cómo expresa el context | DS ejemplo | Patrón de nombre |
|---|---|---|---|
| **explicit** | palabra literal (`bg`, `text`, `border`, `surface`) | color-ramp, Atlassian, Polaris, Fluent, **Nord** | `{role}-{context}-{intensity}[-{state}]` |
| **pair** | implícito en el par `X` / `onX` (rol pelado = fondo, `on` = primer plano) | Material 3 | `primary` / `onPrimary` |
| **step-numbered** | sustituido por un número sobre un rol semántico | Carbon, SLDS | `interactive-01`, `background-hover` |

**Gotcha clave** (**ADR-009**): el problema NO era el formato (separador/casing ya se normalizaban). Era el **vocabulario y el mecanismo**. `interactive-01` terminaba en número → se confundía con una primitiva Tier 1. La distinción la hace el mecanismo: en step-numbered, `{rol-semántico}-NN` es contextual; `{hue}-NN` es primitiva.

**El mecanismo se auto-detecta** cuando es inequívoco (ratio de `onX` pares → pair; `rol-NN` sistemático → step-numbered) y es **override-able** a mano. Default `explicit` = cero regresión.

### Generar en el patrón del sistema

Una vez que tienes el AST, generar Tier 3 en el patrón del sistema destino es serializar:
- **explicit** → `brand-bg-weak-hover` (o `brandBgWeakHover` si la convención es camelCase).
- **pair** → `primary` (fondo), `onPrimary` (primer plano), `primaryStrong`.
- **step-numbered** → `brand-700` (base), `brand-hover` (estado).

La convención (kebab / camel / dot) **se detecta de los tokens importados**, no se asume. Las intensidades y steps salen de tu receta, no del catálogo del DS destino — el composer emite el *patrón*, no reproduce el catálogo exacto.

---

## 4 · El audit loop (validar intent, no existencia)

El audit no pregunta "¿tu naming matchea el patrón?". Pregunta, por token: **¿declara intent legible y resuelve a un valor real?**. El loop:

1. **Detectar mecanismo** (1 vez sobre el set completo).
2. **Clasificar** cada token en tier (primitive / semantic / contextual) por nombre + grafo de referencias.
3. **Correr reglas** que son `constraint` (deterministas: hardcoded-value, broken-ref, circular-ref), `guideline` (lookup) o que dependen del grammar declarado (`broken-progression` solo si `stateGrammar: adjacent`).
4. **Ofrecer fixes** — seguros (auto-aplicables) vs. con riesgo (heurísticos, confirmación) vs. opt-in no-canónicos (placeholder interpolado).

**Principio replicable**: las reglas que dependen de un modelo (Tier 3, progresión adyacente) deben ser **opt-in según el grammar declarado**, no globales. Forzarlas genera falsos positivos en los 2/3 de DS que no usan ese modelo (**audit:176-178**).

---

## 5 · Validación multibrand (el mandato cumplido)

CLAUDE.md exige ≥2 case studies que validen multibrand antes de que un patrón sea canon. **Cumplido**:

| Prueba | Qué demuestra |
|---|---|
| **color-ramp** (case #1) | Sistema generativo, gramática `{rol}-{contexto}-{intensidad}`, dominio color. |
| **Nord** (case #2, **README**) | Sistema curado, semántico-first, `--n-color-*`, dominio salud, AI-native (16/16). |
| **Harness de 6 DS** (**audit-fixtures.mjs**) | Atlassian, Carbon, Fluent, Material, Nord, shadcn — **3 mecanismos** (explicit/pair/step-numbered), 3 convenciones (kebab/camel/dot), todos **PASS**. |

**[INTERPRETACIÓN]** La prueba multibrand no es "el framework funciona con 2 brands del mismo tipo". Es: el **mismo clasificador, sin tocar el vocab**, entiende `brand-bg-weak-hover` (color-ramp, generativo) y `--n-color-border-hover` (Nord, curado) con el mismo mecanismo `explicit`. Y entiende `primary`/`onPrimary` (Material) cambiando solo el mecanismo, no el código. Esa invariancia ante brand/dominio/mecanismo es la definición operacional de multibrand.

---

## 6 · Checklist replicable

Para hacer tu DS legible por agentes sin reescribir tu naming:

- [ ] **No cambies tu naming primero.** Identifica qué mecanismo usas (explicit / pair / step-numbered).
- [ ] **Enumera tu vocab** por axis (role, context, intensity, state). Listas finitas y documentadas (**audit criterio 4**).
- [ ] **Declara tu grammar** una vez (`mechanism` + `axisOrder` + `vocab`). Deja que el formato (separador/casing) se detecte.
- [ ] **Audita por intent**: corre solo las reglas que aplican a TU modelo. Si no tienes Tier 3 contextual, no actives `broken-progression`.
- [ ] **Expón el resultado en un delivery mechanism estándar** — `llms.txt` + Agent Skills (como Nord) o un `_usage` block. El formato sigue al delivery, no al revés (Diana).
- [ ] **Valida con ≥1 sistema distinto al tuyo** antes de declarar un patrón canon (el mandato multibrand).

---

## Referencias

- **ADR-009** — modelo de axes, los 3 mecanismos, decisión.
- **ai-readiness-audit.md** — 24 DS, 8 patrones, scores.
- **case-studies/nord** — case study #2, validación del extremo AI-native.
- `sources/06-...:163` (Cristian, v1/v2) · `sources/09-...:65,131,134` (Diana, intent→delivery→format).
- Implementación viva: `framework/tools/token-grammar-composer.html` (composer v0.5), `audit-fixtures.mjs` (harness).
