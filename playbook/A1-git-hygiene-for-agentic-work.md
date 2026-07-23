---
phase: "Apéndice A — meta (no es una fase de la metodología)"
primary_intent: constraint
secondary_intents: [workflow, framing]
audience: "Cualquiera que construya un DS AI-ready con agentes que generan artefactos en paralelo"
prerequisites: "Repo git del DS; trabajo con agentes / worktrees / sesiones que producen archivos"
sources:
  - sources/10-grace-han-how-to-get-buy-in-for-an-ai-ready-design-system.md:152 (AI amplifica la estructura existente)
  - sources/08-cristian-morales-part-8-what-building-agentic-systems-taught-me.md:34 (speed sin support structures = debt)
  - sources/06-cristian-morales-part-6-encoding-governance-on-agentic-design-systems.md:38 (enforcement automático, no prescripción)
  - sources/07-cristian-morales-part-7-the-human-layer-in-agentic-design-systems.md:82 (encode decisions = accountability)
status: "v1 — escrito desde 4 incidentes reales (2026-06-13) + untrackedHTMLLint de color-ramp (Loop 7)"
---

# A1 — Git hygiene para trabajo agéntico: que el trabajo no se vare

> **Apéndice meta.** No es una fase de la metodología AI-ready (caps 00-99). Es una condición de borde de *cómo se construye* el DS cuando hay agentes produciendo en paralelo. Si tu trabajo se pierde, ninguna fase importa.

## Cita verbatim

> "AI amplifies whatever structure already exists. If the structure is mature, AI becomes leverage. If the structure is weak, AI becomes volatile."
> — `sources/10-grace-han-how-to-get-buy-in-for-an-ai-ready-design-system.md:152`

> "Speed without the support structures to match it doesn't produce more output. It produces more debt. Faster."
> — `sources/08-cristian-morales-part-8-what-building-agentic-systems-taught-me.md:34`

## [MI SÍNTESIS]

Grace habla de la **estructura del DS**, pero el principio se aplica un nivel más abajo: a la estructura de tu **repositorio**. El trabajo agéntico genera artefactos en muchas superficies — worktrees de agente, sesiones que escriben HTML/JSON, branches de exploración, stashes automáticos. Cada superficie es un lugar donde el trabajo puede quedar **fuera de la branch canónica** y volverse invisible.

Esto NO es "git 101". Es específico de DS agénticos: el volumen y el paralelismo del output agéntico **amplifican** el riesgo de stranding. Un humano produce un archivo a la vez en un working dir; un fleet de agentes produce en N worktrees simultáneos. La velocidad sin la estructura de soporte (Cristian `08:34`) no produce más sistema — produce más deuda, más rápido. La deuda aquí es trabajo perdido o duplicado.

La tentación es resolverlo con disciplina: "acuérdate de commitear". Pero la disciplina es exactamente lo que falla bajo la velocidad agéntica. La respuesta AI-ready es la misma que Cristian aplica a la governance del DS:

> "infrastructure where AI doesn't propose screens but enforces rules. […] encoded as executable constraints the system applies automatically, every time, without me."
> — `sources/06-cristian-morales-part-6-encoding-governance-on-agentic-design-systems.md:38`

**Encode la regla "no pierdas trabajo" como un check automático, no como un recordatorio.** Y commitear deja de ser higiene mecánica: es la versión repo de "encode decisions = accountability" (`sources/07-...:82`) — el trabajo no commiteado es una decisión que nadie puede auditar ni recuperar.

## Patrón replicable

Tres capas de enforcement, de la más barata a la más amplia:

### Capa 1 · Untracked en el árbol actual (Stop hook)
Un hook `Stop` (`.claude/hooks/check-untracked.sh`) que al terminar cada turno avisa si hay archivos `??` sin commitear. Barato, frecuente, atrapa el caso común.

### Capa 2 · Trabajo varado fuera del árbol (drift-audit)
El Stop hook solo ve el working dir actual. Un `drift-audit` (`.claude/hooks/drift-audit.sh`) barre lo que el hook no ve:
- **branches** con commits no integrados a la default (`git rev-list --count main..<branch>`),
- **stashes** — incluyendo su **árbol untracked** (`git show <stash>^3`), invisible a `git stash show` a secas,
- **worktrees** extra con cambios sin commitear.

### Capa 3 · El ritual de cierre (/close)
Una skill `/close` que al cerrar sesión corre: git-first → drift-audit → handoff con **git-state real** → memoria → verificación de tree limpio. Convierte "cerrar bien" de intención en checklist ejecutable.

**Principio transversal — "look before you delete":** nunca dropees un stash, borres una branch, ni quites un worktree sin inspeccionar su contenido primero. La existencia de un path **no** garantiza que el contenido sea el mismo (ver Gotchas).

## Ejemplo concreto (4 incidentes en una sesión)

`docs/RETRO-2026-06-13-consolidation.md`: el mismo root cause — trabajo fuera de `main` — golpeó 4 veces en una sesión:

| # | Trabajo varado | Dónde | Cómo se descubrió |
|---|---|---|---|
| 1 | Composer v0.5 | nunca commiteado | se perdió → reconstruido de transcripts |
| 2 | Playbook (9 caps) | 2 forks paralelos | al ir a escribir caps que "faltaban" |
| 3 | 30+ archivos (ADRs, research, case study) | árbol untracked de un stash | al ir a *dropear* el stash "ya consolidado" |
| 4 | CANON.md + 5 ADRs | worktree de agente | al ir a *force-delete* la branch |

Color-ramp ya había encontrado el patrón un año antes: su audit `untrackedHTMLLint` (Loop 7) forzaba commit antes de rotar worktrees. La lección se generaliza: **verificar que el estado está commiteado antes de cualquier operación que pueda perder untracked.**

## Gotchas / edge cases

- **`git stash show` oculta el árbol untracked.** Un stash creado con `-u` guarda los untracked en un 3er padre (`<stash>^3`). `git stash show` muestra solo los tracked → parece vacío cuando tiene 30 archivos. Usar `git stash show -u`. (Incidente #3.)
- **Existencia de path ≠ mismo contenido.** Un check `git cat-file -e main:<path>` da ✓ aunque la versión difiera. Un composer v0.3 (4176 líneas) dio "ya en main" cuando main tenía v0.5 (2556). Comparar **hash/diff**, no solo existencia, antes de descartar.
- **Worktrees de agente acumulan branches no-mergeadas.** Su contenido único (archivos grafteados ≠ commits) puede no ser ancestro de main aunque los archivos sí estén. `git branch -d` lo rechaza con razón — inspeccionar antes de `-D`.
- **El handoff que miente.** Si un handoff dice "X está aquí" pero `git status` dice `?? X`, esos dos hechos coexisten sin confrontarse. El drift-audit los confronta. (Es la contradicción que perdió el composer.)

## Checklist replicable

1. [ ] Hook `Stop` que avisa de untracked en el árbol actual.
2. [ ] `drift-audit` que barre branches/stash/worktrees (incl. `<stash>^3`).
3. [ ] Ritual `/close` que corre drift-audit + handoff con git-state real.
4. [ ] Regla "look before you delete" — inspeccionar contenido antes de dropear/borrar/force-delete.
5. [ ] Handoff que **pega el `git status` literal** al cerrar (no "el archivo está aquí").
6. [ ] (Opcional) En el audit del DS mismo, un lint tipo `untrackedHTMLLint` que falle si hay artefactos críticos sin trackear.

## Referencias

- AI amplifica estructura: `sources/10-grace-han-how-to-get-buy-in-for-an-ai-ready-design-system.md:152`
- Speed sin support = debt: `sources/08-cristian-morales-part-8-what-building-agentic-systems-taught-me.md:34`
- Enforcement automático, no prescripción: `sources/06-cristian-morales-part-6-encoding-governance-on-agentic-design-systems.md:38`
- Encode decisions = accountability: `sources/07-cristian-morales-part-7-the-human-layer-in-agentic-design-systems.md:82`
- Incidentes + prevención: `docs/RETRO-2026-06-13-consolidation.md`, `.claude/hooks/drift-audit.sh`, `.claude/skills/close/SKILL.md`
