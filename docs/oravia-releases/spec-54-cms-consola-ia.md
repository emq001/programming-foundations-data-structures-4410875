# Spec 54 — Consola de IA en el CMS

**Release:** 6 (R6.1 y R6.2) · **Esfuerzo:** M · **Dependencias:** specs 50, 51, 52
**Nota de arquitectura:** este spec convierte el mini-refactor del CMS en tarea obligatoria (ver §Riesgo).

## Contexto

`apps/admin-cms/index.html` es un único fichero HTML que ya carga once consultas en paralelo en su arranque y concentra las colas de Diario, Historias, Parroquias, Comunidad, Moderación, Eventos, Altas in situ, Diócesis, Auditoría y Desarrollo. R9.1 le sumará las de donaciones. Añadir la consola de IA sin partir el fichero es acumular deuda sobre deuda: el plan de R8/R9 ya calificaba el mini-refactor de «necesario», y este spec lo hace **bloqueante**.

## Bloque A — Banco de respuestas aprobadas

### HU-A1 — Responder yo mismo lo que más se pregunta
Como revisor teológico, quiero redactar y aprobar las respuestas frecuentes, para que la mayoría de las consultas no dependan de un modelo.

* **AC-A1.1** — Pestaña «IA» con el CRUD de `ai_answer_bank`: pregunta canónica + variantes, respuesta es/en, fuentes asociadas del corpus, estado editorial.
* **AC-A1.2** — Una respuesta llega a `published` **solo** por el circuito existente: `content_review_status` + firma de `revisor_teologico` (trigger `enforce_review_transition`). No hay atajo para la IA. Test SQL: `test('AC-A1.2: aprobar una respuesta sin revisor teológico falla')`.
* **AC-A1.3** — Vista previa de cómo se verá la respuesta en la app, con sus citas.

## Bloque B — Cola de revisión y retirada

### HU-B1 — Ver qué está contestando la app
Como revisor, quiero muestrear lo que la IA responde y corregirlo.

* **AC-B1.1** — Cola de muestreo alimentada por `ai_answer_log` (G5.2): pregunta, respuesta, fragmentos citados, versión del prompt y modelo. Acciones: **aprobar** (promueve al banco), **corregir** (edita y promueve) y **retirar**.
* **AC-B1.2** — **Retirada en caliente** (G5.3): marcar `retired` bloquea esa pregunta y sus equivalentes por similitud desde la consulta siguiente, sin desplegar código. Test: `test('AC-B1.2: retirar en el CMS surte efecto en la siguiente consulta')`.
* **AC-B1.3** — Toda acción queda en la auditoría editorial existente (`content_review_log`), con actor y fecha, como cualquier otra transición de contenido.

## Bloque C — Materias reservadas y backlog editorial

* **AC-C1.1** — Gestión de `ai_reserved_topics` (G3): alta, baja y edición del mensaje de derivación, en es/en. Cambiar la lista no requiere despliegue.
* **AC-C1.2** — **Vacíos de cobertura**: panel con los temas más preguntados que devolvieron `unknown`, agregados y anónimos (nunca el texto literal de la consulta de un usuario identificable). Es el backlog editorial que pidió el AC-C1.2 del spec 50: la IA no inventa contenido, lo pide.

## Bloque D — Métricas y presupuesto

* **AC-D1.1** — Tiles del día y del mes: consultas, % servidas desde banco, % desde caché, % `unknown`, % muestreadas y revisadas, coste acumulado y proyección de cierre de mes (AC-D1.4 del spec 52).
* **AC-D1.2** — Estado del tope global bien visible, con el modo actual del motor (**generativo** o **degradado a recuperación**) y el botón para degradar manualmente.
* **AC-D1.3** — La pestaña Desarrollo actualiza la línea del Release 6 con su avance real, como se hace con cada entrega.

## Riesgo y precondición

* **Precondición**: partir `apps/admin-cms/index.html` en módulos (al menos: arranque/sesión, colas de moderación, contenido editorial, consola de IA) **antes** de añadir esta pestaña. Sin ello, el arranque del CMS pasa de once consultas paralelas a más de quince y cualquier cambio afecta a todo.
* La consola de IA es la pieza que hace operable el guardrail G5; si se recorta, R6.2 pierde su red de seguridad humana y **no debe publicarse**.

## Verificación

* Un test por AC con su identificador; suite RLS que confirme que solo roles editoriales leen `ai_answer_log`, `ai_answer_bank` y `ai_reserved_topics`.
* Prueba end-to-end en staging: pregunta en la app → aparece en la cola → se corrige y aprueba → la siguiente consulta idéntica se sirve desde el banco sin coste.
* Prueba de retirada: respuesta retirada → la app deja de darla en la consulta siguiente.
