# OraVia — Plan del Release 6 «IA católica confiable»

**Fecha:** 2026-08-09 · **Estado:** propuesta para el dueño del producto · **Base de código analizada:** `emq001/oravia` @ `f911a45` (v0.1.7+10, 203 commits, 40 migraciones, 67 pantallas, 338 casos de test)

Este documento responde a «¿qué se requiere para el Release 6?». Parte de la única definición que hoy existe del R6 —la del panel de Desarrollo del CMS— y la convierte en un alcance verificable, con sus gates, specs (50–55), orden, coste y riesgos.

> **Release 6 — IA católica confiable — sin arrancar (requiere corpus curado + 6 guardrails doctrinales). 0%.**
> — `apps/admin-cms/index.html`, pestaña Desarrollo, corte al 8-ago-2026

---

## 1. Punto de partida: por qué el R6 sigue en 0%

R6 es el único release de la serie que **no tiene una sola línea de código** en el repositorio: `R7` aparece 9 veces en el código (panel de diócesis), `R8` 90 veces, `R9` 47 — `R6` aparece **cero** veces. No es un release a medias: es un release sin empezar, y la razón está en su propia definición: no se puede construir una IA católica confiable sobre un corpus que todavía no está aprobado.

Estado de la serie al 9-ago-2026 (fuente: panel de Desarrollo del CMS + verificación en código):

| Release | Estado | Nota relevante para el R6 |
|---|---|---|
| R1–R3 | 100% | Base de oración, Reconciliación, parroquias, puntos. |
| R4 «Aprender la fe» | 96% | **Falta la revisión catequista** y la licencia del Oficio → es el gate del corpus. |
| R5 / R5.1 | 75% / 88% | Faltan horarios reales de Misa; 82 de 100 Historias de la Biblia. |
| **R6 «IA católica»** | **0%** | Este documento. |
| R7 «Escalamiento institucional» | 50% | Pestaña Diócesis activa; faltan asignaciones, patrocinio, integraciones. |
| R8 / R8.1 / R9 | 100% | Cerrados el 8-ago (specs 38–48). |
| R9.1 «Manos abiertas» | 0% | Spec listo, **bloqueado** por la validación legal del disclaimer. |

## 2. El hallazgo que manda en este release

El corpus que alimentaría a la IA **existe y es abundante, pero está marcado como no apto para publicación**. En el propio código:

```
apps/mobile/lib/data/bundled/catechism_content.dart:
  /// TODO EL CONTENIDO ES DE DESARROLLO PROPIO Y ESTÁ **PENDIENTE DE
  /// DICTAMEN TEOLÓGICO** (#23): no apto para beta pública hasta su revisión.
```

La misma marca aparece en `reconciliation_content.dart`, `night_reflection_content.dart`, `formation_courses.dart`, `family_content.dart`, `advent_content.dart` y `daily_phrases.dart`. Y en los textos visibles al usuario (`app_localizations_es.dart`):

* Catecismo: *«redacción propia, provisional y pendiente de dictamen»* — 120 entradas en 4 pilares, con referencia numérica al CIC pero **sin reproducir su texto** (derechos LEV).
* Biblia: *«Traducción Torres Amat (1825, dominio público), provisional y pendiente de dictamen; los deuterocanónicos están en preparación»* — 66 libros es/en, 9.1 MB.

Conclusión operativa: **el R6 no empieza por el modelo, empieza por el dictamen.** Una IA que cita un corpus provisional propaga con autoridad de máquina lo que hoy la app ya advierte como provisional. Ese es exactamente el riesgo que el nombre del release («confiable») pretende evitar.

## 3. Los 6 guardrails doctrinales

La definición del CMS exige «6 guardrails doctrinales» sin enumerarlos; el detalle vivía en `18-roadmap.md`, que no está en el repositorio (los 30 documentos de especificación viven fuera, en `../docs/specs/`). **Esta es la propuesta de los seis**, derivada de la gobernanza que el proyecto ya aplica (trigger editorial, ADR-004, ADR-007). *Debe contrastarse con `18-roadmap.md` antes de congelar el alcance; si el roadmap original enumeraba otros, mandan los del roadmap.*

| # | Guardrail | En una línea |
|---|---|---|
| **G1** | **Nunca sin fuente** | Toda respuesta se funda en fragmentos recuperados del corpus y muestra su cita; sin evidencia suficiente, la respuesta es «no lo sé» + derivación. |
| **G2** | **Corpus cerrado y aprobado** | Solo entra contenido `published` con dictamen de revisor teológico y licencia declarada. El modelo no aporta conocimiento propio ni cita de memoria. |
| **G3** | **Materias reservadas** | Lista explícita de temas que la IA no responde y deriva a un sacerdote o a la parroquia. |
| **G4** | **El asistente no hace de sacerdote** | No absuelve, no impone penitencia, no habla en nombre de Dios, no discierne vocaciones ni autoriza sacramentos. |
| **G5** | **Revisión humana en el bucle** | Banco de respuestas aprobadas, muestreo obligatorio de las libres, y potestad del revisor teológico de retirar y bloquear una respuesta. |
| **G6** | **Privacidad sagrada** | Diario, Reconciliación, notas cifradas, intenciones privadas y ubicación **jamás** entran en un prompt. ADR-004 queda intacto. |

Detalle verificable de cada uno en `spec-51-guardrails-doctrinales.md`.

## 4. Alcance propuesto: specs 50–55

La serie de specs continúa donde la dejó el R9.1 (38–49).

| Spec | Documento | Qué resuelve | Esfuerzo |
|---|---|---|---|
| 50 | `spec-50-corpus-curado.md` | Saneamiento doctrinal y licencias; tabla de corpus con procedencia y embeddings (pgvector). | **XL** (mayoría es trabajo humano, no código) |
| 51 | `spec-51-guardrails-doctrinales.md` | G1–G6 con criterios de aceptación testeables. | M |
| 52 | `spec-52-motor-respuesta.md` | Edge Function `ask-catholic`: recuperación, caché, cuotas, coste acotado. | L |
| 53 | `spec-53-ux-preguntar.md` | «Preguntar» en la app: entrada, respuesta con citas, derivación, i18n es/en. | M |
| 54 | `spec-54-cms-consola-ia.md` | Consola de IA en el CMS: banco de respuestas, cola de revisión, incidentes, métricas. | M |
| 55 | `spec-55-privacidad-legal-ia.md` | ADR-011, política de privacidad, subencargado de tratamiento, retención, presupuesto. | S (código) / M (legal) |

## 5. Recomendación de arranque: partir el R6 en dos

El precedente de la casa (R3.5–R3.11, R5.1, R8.1, R9.1a/b) admite sub-releases. Aquí el corte tiene además valor de riesgo:

* **R6.1 «Buscar en la fe» — sin modelo generativo.** Búsqueda semántica sobre el corpus aprobado: el usuario pregunta en lenguaje natural y recibe **fragmentos reales, citados y ya revisados**, sin texto generado. Cubre G1, G2 y G6 por construcción —no hay nada que alucinar— y entrega la mayor parte del valor percibido. Specs 50, 52 (rama de recuperación), 53, 54.
* **R6.2 «Responder en la fe» — con modelo generativo.** El modelo redacta una síntesis catequética **solo** sobre los fragmentos recuperados, con G3, G4 y G5 activos y muestreo humano continuo. Specs 51 y 52 (rama de generación) completos, más el 55.

Si el calendario o el presupuesto aprietan, R6.1 se puede publicar y sostener indefinidamente sin R6.2. Lo contrario no es cierto: R6.2 sin el corpus del 50 no debe existir.

## 6. Orden de implementación

| Orden | Trabajo | Bloqueante | Quién |
|---|---|---|---|
| 0 | **Dictamen teológico del corpus** (Catecismo 120 entradas, formación, reconciliación, familia, reflexión nocturna, frases diarias) — cierra también el 4% pendiente del R4 | Sí, para todo lo demás | Revisor teológico / catequista |
| 1 | Spec 50 — tabla `corpus_chunk`, pgvector, ingesta con procedencia y licencia | Sí, para 52 | Ingeniería |
| 2 | Spec 55 — ADR-011 y decisión de proveedor + privacidad | Sí, para 52 (si sale del país datos) | Dueño + abogado |
| 3 | Spec 52 rama recuperación + Spec 53 + Spec 54 → **publica R6.1** | — | Ingeniería |
| 4 | Spec 51 — guardrails codificados y suite de evaluación doctrinal | Sí, para 52 generativo | Ingeniería + revisor |
| 5 | Spec 52 rama generación → **publica R6.2** | — | Ingeniería |

## 7. Coste, la variable nueva del proyecto

Hasta hoy el único coste variable por uso es la síntesis de voz, y está acotado por diseño: `generate-audio` solo sintetiza lecturas reales y clips de una lista blanca, y cachea por hash de texto — **solo el primer oyente paga**. El R6 rompe ese patrón: una pregunta libre es, por definición, texto nuevo.

El spec 52 traslada la misma disciplina al motor: caché por pregunta normalizada, banco de respuestas aprobadas que se sirve sin llamar al modelo, cuota diaria por usuario y tope global de gasto con degradación a R6.1 (solo recuperación) cuando se alcanza. **Sin un tope duro configurable, este release no debe habilitarse en producción.**

## 8. Riesgos

* **Doctrinal** — el más alto del release y la razón de su nombre. Mitigación: G1–G5, corpus cerrado, muestreo humano y potestad de retirada inmediata (spec 54).
* **Reputacional y pastoral** — una respuesta desafortunada sobre confesión, duelo o crisis personal daña más que cualquier fallo técnico. Mitigación: G3 (materias reservadas) y la pauta de crisis del spec 51.
* **Legal / privacidad** — enviar texto del usuario a un proveedor externo introduce un subencargado de tratamiento que la política de privacidad actual no contempla. Mitigación: spec 55; y G6 como línea que no se cruza (ADR-004 no se toca).
* **Licencias** — el corpus mezcla redacción propia, dominio público (Torres Amat 1825) y referencias numéricas al CIC. La IA **no puede** reproducir texto del CIC ni de traducciones con derechos; el corpus se ingiere con `license_type` y la ingesta rechaza fuentes sin licencia declarada, igual que hace hoy el trigger editorial.
* **Coste descontrolado** — ver §7.
* **CMS de un solo HTML** — `apps/admin-cms/index.html` ya carga 11 consultas en paralelo y suma las colas de R8/R9/R9.1. La consola de IA (spec 54) hace del mini-refactor del CMS una tarea **inevitable**.

## 9. Decisiones que necesita el dueño del producto

| # | Decisión | Por qué bloquea |
|---|---|---|
| **D-R6-1** | ¿Se confirman estos 6 guardrails, o mandan los de `18-roadmap.md`? | Define el alcance del spec 51. |
| **D-R6-2** | ¿R6.1 y R6.2, o un solo R6 generativo? | Define calendario, coste y perfil de riesgo. |
| **D-R6-3** | Proveedor del modelo y de los *embeddings*, y si se acepta que el texto de la consulta salga del país. | Bloquea el spec 55 y la política de privacidad. |
| **D-R6-4** | Presupuesto mensual tope y qué pasa al agotarse (degradar a recuperación / cerrar la función). | Bloquea el spec 52. |
| **D-R6-5** | ¿Quién firma el dictamen del corpus y en qué plazo? | Bloquea todo el release. |
| **D-R6-6** | Prioridad relativa: R6 frente a cerrar R4 (revisión catequista), R5 (horarios de Misa), R5.1 (82 historias), R7 (diócesis) y desbloquear R9.1. | El dictamen del corpus es trabajo compartido con R4: hacerlos juntos ahorra una pasada completa del revisor. |

## 10. Verificación transversal

Se mantienen las reglas de la casa: un test por AC nombrado con su identificador, paridad i18n es/en de toda clave nueva, contraste AA para todo visual nuevo, suite RLS en SQL por tabla nueva y la garantía de que nada cifrado viaja ni queda en claro. El R6 añade dos exigencias propias:

* **Suite de evaluación doctrinal** (spec 51): un conjunto fijo de preguntas —incluidas las trampa y las de materias reservadas— que corre en CI y falla el build si una respuesta pierde su cita, entra en materia reservada o contradice el corpus.
* **Test de fuga de privacidad** (spec 55): verifica que ninguna ruta del motor puede leer `journal_entry`, notas de Reconciliación ni `rosary_session.intention_note`, que viven solo en la base local cifrada.

## 11. Índice de specs

| Spec | Documento | Título |
|---|---|---|
| 50 | `spec-50-corpus-curado.md` | Corpus curado: dictamen, licencias e ingesta |
| 51 | `spec-51-guardrails-doctrinales.md` | Los 6 guardrails doctrinales |
| 52 | `spec-52-motor-respuesta.md` | Motor de respuesta `ask-catholic` |
| 53 | `spec-53-ux-preguntar.md` | «Preguntar» en la app |
| 54 | `spec-54-cms-consola-ia.md` | Consola de IA en el CMS |
| 55 | `spec-55-privacidad-legal-ia.md` | Privacidad, legal y coste de la IA |
