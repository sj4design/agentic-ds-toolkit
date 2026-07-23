---
title: "07 — Component contracts: Formalise en la práctica"
phase: "C / Formalise (concreto)"
status: "redactado desde el case study Link FieldIQ (sesiones 2026-06-19 → 21)"
---

# 07 — Component contracts: Formalise en la práctica

> Este capítulo es el **par concreto** de [`03-formalise.md`](03-formalise.md) (*components as APIs*, que es teoría + manifest abstracto). Acá está el **método probado**: cómo tomar un componente de un DS real **curado a mano y NO nacido AI-ready** y producir un **contrato AI-ready** + su verificación en vivo. Validado contratando 6 componentes del DS de **Link FieldIQ** (ver **`case-studies/link-fieldiq/`**).

---

## Dónde encaja en el arco

El framework completo (ver [`AI-READY-ROADMAP.md`](../AI-READY-ROADMAP.md)) son 4 fases Grace Han + capa humana:

| Fase | Capítulo | Estado |
|---|---|---|
| A — Stabilise | [01](01-stabilise.md) | ✅ |
| B — Structure (capa semántica + intent) | [02](02-structure.md) | 🟡 |
| **C — Formalise (components as APIs)** | [03](03-formalise.md) teoría · **07 práctica** | el manifest abstracto ahora tiene **método probado** |
| D — Enforce (rules-as-data) | [04](04-enforce.md) | 🔴 |
| Capa humana / token grammar / buy-in | [05](05-human-layer.md) · [06](06-token-grammar-and-audit.md) · [99](99-buy-in.md) | — |

El **token grammar audit** (cap. [06](06-token-grammar-and-audit.md)) opera a nivel **token**; este capítulo opera a nivel **componente**. Son complementarios: el token cascade de cada contrato (abajo) se apoya en el inventario de tokens del cap. 06.

---

## 1 · El pipeline replicable

Contratar un componente NO es escribir un markdown. Es un pipeline de 5 pasos, **en orden, con un gate**:

```
inspect → PRE-FLIGHT AUDIT (gate) → contrato → parity render → ADR (si emerge patrón)
```

1. **Inspect** — extraer la verdad del componente desde la fuente (Figma), no desde memoria. Todo valor sale de inspección real (`figma-console-mcp`). Lo no-verificable se marca `[?]`.
2. **Pre-flight audit** *(gate — §2)* — auditar ANTES de escribir el contrato. Si no se audita, el contrato hereda los defectos del componente sin nombrarlos.
3. **Contrato** *(§3)* — el markdown con building blocks, axes, token cascade, etc. Los hallazgos del audit van AL contrato.
4. **Parity render** *(§5)* — reproducir el componente en HTML/CSS real con los tokens exactos, lado a lado contra la ref de Figma. Verifica que el contrato sea fiel y revela desalineaciones al pixel.
5. **ADR** — si emerge un patrón estructural (familia, composición, regla de binding), se documenta en un ADR; el contrato lo referencia.

**Regla de oro:** *contrato ⇒ parity, misma tanda.* Nunca son pasos separados. Un contrato sin render es una afirmación sin prueba.

---

## 2 · El gate: pre-flight audit antes del contrato (ADR-016)

> Decisión: **ADR-016**. Skill: **`framework/skills/component-audit`**.

Antes de escribir un solo campo del contrato, correr:

- `figma_lint_design` — WCAG + design-system + layout.
- `figma_audit_component_accessibility` — scorecard a11y (coverage, focus, target, color-blind).
- `figma_analyze_component_set` — máquina de variantes (axes, diffs).
- **Checklist manual** de 4 ejes que las tools no detectan:
  - **primitive** — ¿el componente bindea a un primitive (`Palette/*`) donde debería un token semántico? (cascade violation)
  - **coupling** — ¿usa el namespace de OTRO componente? (ej. el toggle usaba `Button/Primary/*`)
  - **asimetría** — ¿estados/axes inconsistentes entre hermanos? (checkbox tiene Loading, radio no)
  - **parent-governed** — ¿qué "gaps" (hover/press/target-44) NO son del átomo sino del parent?

**Por qué es un gate y no un paso opcional:** sin el audit, el contrato describe el componente *como si estuviera bien*. Con el audit, el contrato nombra la deuda (`🔴`/`🟡`/`🟢` por severidad) y se vuelve accionable. Cada hallazgo se traslada a la § *Pre-flight audit* del contrato Y a su bloque de auditoría en el parity.

---

## 3 · Anatomía del contrato

Un contrato de componente tiene estas secciones (ver cualquiera en **`contracts/`**):

| Sección | Qué captura |
|---|---|
| **TL;DR + Use case** | qué es, cuándo usarlo, cuándo NO (link a hermanos) |
| **Building blocks** (schema v1) | cada sub-pieza con `kind` (composition/token/external_lib), `access_level` (public/internal/external), y `own_contract` (**inline** vs **archivo/§ propia** vs **referenciado**) |
| **Axes** | las VARIANT/BOOLEAN/TEXT properties, defaults, y notas de asimetría |
| **Anatomy** | la estructura por estado, valores reales |
| **Token cascade** | token bound → capa → valor resuelto **por modo** (Base/Accent), con flags de deuda |
| **Composition rules + anti-patterns** | qué se permite / qué nunca |
| **Theme behavior** | resolución por theme (revela si es theme-aware) |
| **Pre-flight audit** | tabla de hallazgos por severidad + scorecard a11y + ARIA propuesto |
| **Implicit conventions discovered** | los patrones que el DS usa pero no documenta (oro para el framework) |
| **Done criterion + References** | checklist + Figma ids + links |

La pieza más reutilizable es el **building-blocks schema** + la regla **inline-vs-propio** (cuándo un sub-componente merece su propia documentación) — formalizada en el §4.

---

## 4 · Los 3 patrones de composición

Contratando 5 componentes emergieron 3 formas distintas en que un componente se relaciona con sus partes:

### 4.1 — Family (hermanos horizontales) · **ADR-017**

Varios componentes **comparten chassis** y especializan. Checkbox + Radio + Toggle = *Selection family*: comparten border token, métrica 24×24, axis binario parent-governed; difieren en forma/indicador. Se escribe **un family contract** (lo compartido) + members que especializan.

- **Señal:** N componentes con el mismo chassis y axis.
- **Hallazgo recurrente:** el DS tenía un namespace `Button/Selection/*` diseñado para ellos, **sin usar** — todos bindeaban a primitives/Button-Primary. La deuda #1 de la familia.

### 4.2 — Layered composition + regla de `.`-internos (vertical) · **ADR-018**

Un componente de producción se construye apilando **subcomponentes `.`-prefijados** (internos/template), cada capa una preocupación. Avatar: `User avatars` (producción) ⊂ `.avatar sizes` (escala) ⊂ `.avatar base` (forma).

- **Regla de contratación** (la pieza promovible):
  1. **`.` (dot) = interno → contratar SOLO producción.** El componente sin `.` recibe el contrato; los `.`-internos son building blocks. (Le dice al agente qué instanciar.)
  2. **Un `.`-interno se gana § propia si carga un sistema de axes** (ej. `.avatar sizes` = 36 variantes); si es hoja, **inline** (ej. `.avatar base`, el `.Selected dot` del radio).
- **Vertical ≠ horizontal:** layered (capas) es distinto a family (hermanos). Un componente puede ser ninguno (leaf).

### 4.3 — Molécula (compone átomos de producción)

Un componente compone **átomos de producción ya contratados** (no `.`-internos). Toast Message compone `Circled button` (átomo público) + iconos + `.dot` + progress bar. El building block es público → se **referencia su contrato**, no se documenta inline.

→ Los 3 patrones se distinguen por el **`access_level` del building block**: `public` (referenciar) · `internal`/`.` (inline o § según axes) · `external` (iconos, lib).

---

## 5 · El parity render: verificar el contrato en vivo

> **`component-parity.html`**. Sirve por el static-preview server (`framework/tools/static-preview.mjs`).

Cada componente contratado se reproduce en **HTML/CSS real con los tokens exactos**, en 4 bloques: ① playground interactivo (live CSS vs Figma ref, con overlay/pixel-diff/redlines/theme/zoom) · ② variantes · ③ tokens & notas · ④ auditoría a11y. Es la **prueba** de que el contrato es fiel.

**Arquitectura (data-driven, anti-drift):**
- Las `<section>` **no se escriben a mano** — se generan desde `SECTIONS[]` con una plantilla única `buildSection()`. El shell estructural vive en **un solo lugar** → imposible mis-anidarlo por componente. Agregar uno = entrada en `COMPONENTS{}` (engine) + entrada en `SECTIONS[]` (markup) + CSS + refs.
- **Self-check `assertLayout()`** — corre en load/cambio/resize; si la estructura driftea (ej. HTML mal anidado que el navegador "recupera" en silencio) o una variante desborda su celda, falla **ruidoso** (console.error + banner). Convierte un bug invisible en uno que el verify step atrapa.

**Técnicas de fidelidad descubiertas** (promovibles):
- **Inspect = nombre de token + valor resuelto**, no el raw hex. `Radius: 48 hardcoded` es raw legítimo (sin token); todo lo demás muestra el token.
- **Figma INSIDE stroke → CSS `inset box-shadow`** (no `border`): el `border` + `box-sizing:border-box` consume 1px y corre los hijos por padding; el inset shadow replica el stroke INSIDE sin consumir layout.
- **Export de refs tight** (read-only): `node.exportAsync({format:'PNG',constraint:{type:'SCALE',value:4}})` + recortar al layout box con `sips --cropOffset` (offset = `(absoluteBoundingBox − absoluteRenderBounds) × 4`). El REST padea; `exportAsync` incluye bleed de sombra. Para iconos: `exportAsync({format:'SVG_STRING'})` + normalizar fill/stroke a `currentColor` (preservando máscaras).
- **Componentes wide** (moléculas de 402px en un playground diseñado para átomos de 24px): `wide` apila los stages full-width, `wideVariants` usa 1 columna, `comp.zoom` da zoom inicial por componente, opción `0.5×`.

---

## 6 · Lo que se construyó (inventario / proof)

Estado al cierre de la sesión 2026-06-21:

| Componente | Patrón | Contrato | Hallazgo headline |
|---|---|---|---|
| Checkbox Button | family member | **✓** | fill = primitive (Cement 900) |
| Radio Button | family member | **✓** | dot no theme-aware (2.30:1 Accent) |
| Toggle | family member (divergente) | **✓** | perilla On se funde con el track en Accent (1.00:1) |
| Avatar | layered composition | **✓** | fill/initials primitives; initials size-locked (27 no-text-style) |
| Toast Message | molécula | **✓** | texto blanco/green = 3.6:1 (falla AA); container usa `Button/Focus stroke` |
| Badge | layered (Color×Style) | **✓** | **contra-ejemplo positivo**: namespace `Badge/*` theme-aware → 10/10 pasan AA; regresión aislada de 3 colores de marca a primitives |

+ **family contract** (**selection-family.md**), **4 ADRs** (016 gate · 017 family · 018 layered · 019 token namespace), el **parity render** data-driven con self-check, y el **inventario de tokens** (**tokens.md v2**, 307 vars) + componentes (**components.md**).

**Patrón recurrente del DS** (señal AI-ready): casi todos los componentes bindean a **primitives donde debería haber un token semántico**, y tienen un namespace dedicado **creado pero sin usar**. El audit lo nombra; el fix es un ADR + cambio en Figma (lo hace el equipo de diseño — Figma read-only desde el case study).

**El contra-ejemplo (Badge):** no todo es deuda. Badge tiene un namespace **dedicado, semántico y theme-aware** `Badge/{Color}/Fill` + `/On-fill` — el **patrón objetivo AI-ready** (**ADR-019**). El par fill/on-fill garantiza contraste: **los 10 colores pasan AA** sin un solo fix. La deuda está **aislada** (3 colores de marca añadidos después que cayeron a primitives) — señal de que la deuda nace en las *extensiones*, no en el diseño original. Badge le da al framework un ejemplar de "así se ve bien", no solo del anti-pattern.

---

## 7 · Gotchas

- **No inventar.** Todo valor sale de inspección real; lo no-verificable es `[?]`. Ante cambios en Figma, re-inspeccionar y diffear, no asumir.
- **Figma read-only.** El case study NO escribe en el archivo Figma; los fixes de binding los hace el equipo (pedir confirmación).
- **El audit es un gate, no un adorno.** Saltarlo produce contratos que describen la deuda como si fuera diseño correcto.
- **`primitive`/`coupling` no los detecta el lint** — son checklist manual (cascade). El lint detecta default-names, contraste, target, auto-layout.
- **Output grande de MCP** (dev tree, base64 de exports) revienta el context — forzar guardado a archivo y procesar con un script, fuera del context.
- **El navegador "recupera" HTML roto en silencio** — por eso el `assertLayout()` self-check; sin él, un `</div>` de más pasa invisible.

---

## 8 · Checklist replicable

Para contratar un componente nuevo:

- [ ] Inspeccionar la fuente (figma-console-mcp); marcar `[?]` lo no-verificable.
- [ ] **Pre-flight audit** (lint + a11y + analyze + checklist primitive/coupling/asimetría/parent-governed). [ADR-016]
- [ ] Identificar el patrón de composición (family / layered+`.`-internos / molécula / leaf) → ADR si es nuevo.
- [ ] Escribir el contrato (building blocks schema · axes · token cascade por modo · theme · composition rules · implicit conventions · audit · done criterion).
- [ ] Decidir inline-vs-§-propio por cada `.`-interno (regla [ADR-018]: sistema de axes → §; hoja → inline).
- [ ] **Parity render misma tanda**: refs tight a `assets/`, entrada en `COMPONENTS{}` + `SECTIONS[]`, CSS de render.
- [ ] Verificar en el browser: `[parity:layout] ok`, sin overflow, live ≈ Figma ref.
- [ ] Actualizar `components.md` (catálogo) + la cola de Phase 3.

---

## Referencias

- Decisiones: **ADR-016** (gate) · **ADR-017** (family) · **ADR-018** (layered + `.`-internos) · **ADR-015** (frontera case study)
- Case study: **`case-studies/link-fieldiq/`** — contratos, parity render, tokens.md v2, components.md
- Teoría de la fase: [`03-formalise.md`](03-formalise.md) (manifest / components as APIs)
- Token-level audit (complementario): [`06-token-grammar-and-audit.md`](06-token-grammar-and-audit.md)
- Skill del gate: **`framework/skills/component-audit`**
