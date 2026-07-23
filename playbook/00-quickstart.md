---
title: "Quickstart — first 30 minutes for a replicating team"
status: "placeholder — addresses Loop F3.1 + F7.10"
intent: "workflow"
audience: "team lead / steward of a new agentic DS"
self_contained: true
---

# Quickstart — Primeros 30 minutos

> Este capítulo asume que **NO has leído `sources/SYNTHESIS.md`** (570 líneas, teoría). Es operacional: qué hacer ahora.

## ¿Eres el lector correcto?

✅ Sí, si:
- Tu equipo tiene un design system existente (o va a construir uno) y quiere hacerlo AI-ready.
- Eres design system steward, lead, o staff IC con autoridad de decisión.
- Tienes ≥3 hrs esta semana para diagnóstico inicial.

❌ No todavía, si:
- Tu DS aún no tiene tokens consolidados (consigue eso primero — Phase 1 Stabilise de Grace Han).
- No tienes un humano accountable (steward) — sin steward, NO continuar.

## Los 30 minutos

### Minuto 0-5 — Identifica tu ruta

El roadmap tiene **dos rutas válidas** (no una). Elige según tu punto de partida:

- **Greenfield** (Diana-style, top-down): no tienes DS legacy, o el legacy se va a deprecar. Empiezas clasificando intent, después decides delivery, después format. Empieza por `playbook/02-structure.md` (Fase A).
- **Retrofit** (Cristian-style, bottom-up): tienes DS legacy en producción. Empiezas inventariando skills/rules/artifacts ya en uso, los reorganizas, después aplicas Fase C. Empieza por inventario.

### Minuto 5-15 — Auditoría SAFE (Grace Han framework)

Clasifica tu DS actual en 4 dimensiones, cada una Low / Medium / High:

| Dimensión | Pregunta |
|-----------|----------|
| **S — Structure** | ¿Hay single source of truth para tokens? ¿Duplicación reducida? |
| **A — Alignment** | ¿Definitions consistentes cross-platform / cross-region? |
| **F — Formalisation** | ¿Decisiones expresadas como contratos governed (no informal docs)? |
| **E — Enforcement** | ¿Standards validated automáticamente en delivery pipelines? |

**La dimensión más baja es tu prioridad real.** Si Structure es Low: NO empieces con AI yet. Stabilise primero (Phase 1 Grace Han).

### Minuto 15-25 — Identifica el steward

**Sin un humano accountable que "encode decisions and know" en vez de "hand off and hope" (cita Cristian Part 7), todo el resto fracasa.**

Signals de latent steward (busca esto en tu equipo):
- Reporta inconsistencias hacia el sistema en vez de hardcodear alrededor.
- Conoce el "por qué" de las decisiones, no solo el "qué".
- Tiene autoridad sobre el DS, no solo permiso de ejecución.

Disqualifying patterns (evita):
- IC ocupado sin decision authority.
- Manager sin hands-on contact con el sistema.
- Asignación por seniority sin volunteering.

Tiempo necesario: **bootstrap 50-75% FTE, steady-state 15-25% FTE** (estimate v0 — calibrar con tu propio cycle).

### Minuto 25-30 — Define tu próximo paso

Solo después de SAFE audit + steward identified:

- Si tu lowest SAFE = **Structure** → fase 1 Stabilise (no AI yet).
- Si tu lowest SAFE = **Alignment** → comienza Fase A (capa semántica).
- Si tu lowest SAFE = **Formalisation** → Fase B (components as APIs) o Fase C (rules as data).
- Si tu lowest SAFE = **Enforcement** → Fase D (linter + CI).

## Después del quickstart

1. Lee el capítulo de tu fase: `playbook/02-structure.md` (A) / `03-formalise.md` (B+C) / `04-enforce.md` (D) / `05-human-layer.md` (E paralelo).
2. Si quieres la teoría: `sources/SYNTHESIS.md`.
3. Si quieres ver una implementación de referencia: `references/cristian-morales/`.
4. Si quieres entender por qué algo está donde está: `docs/adr/`.

## ¿Necesitas más contexto antes de empezar?

- **Por qué AI-ready importa**: Grace Han, "How to make your design system AI-ready" → `sources/11-grace-han-ai-ready.md`.
- **Cómo conseguir buy-in de leadership**: Grace Han, "How to get buy-in" → `sources/10-grace-han-how-to-get-buy-in-for-an-ai-ready-design-system.md`.
- **Por qué la capa humana es prerequisito**: Cristian Morales Part 7 → `sources/07-cristian-morales-part-7-the-human-layer-in-agentic-design-systems.md`.

## Status

Placeholder con esqueleto operacional. Se expandirá conforme las Fases A-E sean implementadas y validadas con case studies.

## Referencias

- AI-READY-ROADMAP.md Pre-flight section.
- Loop finding F3.1 (Analyst 3): pre-flight onboarding ausente.
- Loop finding F7.10 (Analyst 7): DX onboarding requiere ~1500 líneas hoy.
