# Agentic DS Toolkit

Herramientas y metodología para construir un **design system multibrand que un agente de IA
pueda consumir** — no solo leer.

Todo funciona abriendo un archivo en el navegador. Sin instalación, sin servidor, sin internet.

---

## Qué hay aquí

| | Qué hace | Abrir |
|---|---|---|
| **Roadmap adaptativo** | Contestas 2 preguntas y te devuelve **tu** mapa: 135 pasos explicados uno a uno, reordenados y podados según tus respuestas. | [`roadmap.html`](roadmap.html) |
| **Playbook** | Los 11 capítulos de metodología detrás de las herramientas. | [`playbook/`](playbook/) |
| Mapa mental · Mapa de proceso | Dos vistas del mismo modelo de 135 pasos: radial de un vistazo, y acordeón para profundizar. | [`mapa-mental.html`](mapa-mental.html) · [`mapa-proceso.html`](mapa-proceso.html) |

**Por dónde empezar:** abre `roadmap.html`. Las dos primeras preguntas deciden qué te muestra.

---

## Por qué el roadmap pregunta antes de mostrarte nada

Porque **no hay un roadmap único**. Dos preguntas cambian el mapa de verdad:

- **¿Existe ya un design system o arrancas de cero?** — reordena las fases. Quien parte de
  cero empieza definiendo propósito; quien tiene un sistema en producción empieza
  inventariando lo que ya hay.
- **¿Una marca o varias?** — poda 2 partes completas y 15 pasos. En single-brand desaparecen
  el mecanismo de cambio de marca y su prueba de aceptación, que en multibrand es el criterio
  más duro del sistema.

Una tercera pregunta —dónde estás más débil— solo aparece si ya tienes un sistema.

Lo demás **no se pregunta**, porque solo tiene una respuesta: un design system lo consumen
diseñadores, desarrolladores y agentes. Los tres, siempre. Un paso con una sola respuesta
posible no es un paso, es una premisa, y va declarado al principio.

---

## Sobre las evidencias

Las herramientas usan casos reales medidos, no ejemplos inventados: sistemas con la paleta
duplicada en nueve archivos, con 99,85% de adherencia y 59% de cobertura a la vez, con tokens
que compilan y no pintan nada.

**Vienen de auditorías a sistemas en producción y de un case study interno con marcas
simuladas.** No documentan el design system real de ninguna empresa concreta, y no deben
leerse como tal.

---

## Sobre las citas del playbook

Los capítulos citan sus fuentes con `sources/NN-autor-titulo.md:LÍNEA`. Esos archivos **no
están en este repositorio**: son artículos de terceros que viven en el repo privado de
investigación.

Las citas se conservan a propósito. Son la prueba de que cada regla salió de una fuente y no
de una intuición — el protocolo del proyecto prohíbe escribir una regla sin poder señalar de
dónde viene. Los artículos originales están enlazados abajo.

---

## Créditos

Este trabajo se apoya en el de otras personas, y sin ellas no existiría.

**Cristian Morales Achiardi** — serie *Towards an agentic design system*:
- [Building an AI-Ready design system](https://www.designsystemscollective.com/building-an-ai-ready-design-system-35e709f54edf)
- [Towards an agentic design system](https://medium.com/@crmorales.achiardi/towards-an-agentic-design-system-c7e0a6469bb1)
- [Encoding governance on agentic design systems](https://www.designsystemscollective.com/encoding-governance-on-agentic-design-systems-1a8c70420fec)

**Grace Han**:
- [How to make your design system AI-ready](https://medium.com/@gracehan.me/how-to-make-your-design-system-ai-ready-741ae69293df)
- [How to get buy-in for an AI-ready design system](https://www.designsystemscollective.com/how-to-get-buy-in-for-an-ai-ready-design-system-87bb64b3e5a8)

**Diana Wolosin** — *Intent-driven context for AI design systems*. De ahí viene el orden que
gobierna todo el proceso: primero el propósito, después el mecanismo de entrega, y solo
entonces el formato.

El marco **SAFE** (Structure · Alignment · Formalisation · Enforcement) es de Grace Han. Aquí
se usa con una diferencia deliberada: ella lo diseñó para explicarle riesgo a dirección, así
que en vez de pedirte que te autoclasifiques, el roadmap te hace preguntas observables sobre
tu repositorio y deduce el nivel.

---

## Estado

El **playbook está en borrador**. Tres capítulos están redactados (gramática de tokens,
contratos de componente, higiene de git), cinco son borradores con casos pendientes de
completar, y dos son esqueletos. Cada capítulo declara su estado en la cabecera.

El **roadmap sí está probado**: verificado contra sus tres combinaciones de respuestas.

---

## Licencia

[CC BY 4.0](LICENSE) — puedes usarlo y adaptarlo, incluso comercialmente, citando la autoría.
