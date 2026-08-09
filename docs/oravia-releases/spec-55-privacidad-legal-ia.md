# Spec 55 — Privacidad, legal y coste de la IA

**Release:** 6 (bloqueante para R6.2; la parte de privacidad aplica ya en R6.1) · **Esfuerzo:** S en código, M en trabajo legal
**ADRs:** 004 (diario cifrado local) · **ADR-011 (nuevo, este spec)** · **Dependencias:** decisión D-R6-3 del dueño del producto

## Contexto

Hasta hoy el proyecto tiene una postura de privacidad simple de explicar y fácil de defender: lo íntimo no sale del teléfono. `journal_entry` **no existe en el servidor** (ADR-004, cifrado AES-256-GCM con `JournalCrypto` en Drift local), `rosary_session.intention_note` solo vive en la base local, las notas de Reconciliación (spec 44) se cifran con el mismo mecanismo, y la posición del usuario nunca viaja —solo la de un lugar, por acción deliberada (D4)—. Incluso `source_reference.license_details` está protegido por GRANT por columna para que no se filtre por la API.

Una IA conversacional toca esa postura en un punto nuevo: **el texto que el usuario escribe en la caja de preguntas**. Aunque G6 impide que el Diario o la Reconciliación entren en el prompt, nada impide que un usuario escriba en la pregunta algo igual de íntimo. Ese es el objeto de este spec.

## Bloque A — ADR-011: los límites de la IA en OraVia

* **AC-A1.1** — Nuevo `docs/adr-011-ia-catolica.md` en el repositorio de la app, con el formato de la serie (Contexto / Decisión / Garantías / Alternativas descartadas), que fije al menos:
  1. **ADR-004 no se toca**: el contenido cifrado local nunca alimenta un prompt, ni siquiera con consentimiento — no hay interruptor que lo permita.
  2. **Corpus cerrado**: el modelo responde solo sobre contenido aprobado (G1/G2).
  3. **Proveedor y ubicación del tratamiento** declarados, con el país al que sale la consulta.
  4. **Retención**: qué se guarda, cuánto y para qué (ver Bloque B).
  5. **Sin entrenamiento con datos de usuarios**: exigencia contractual con el proveedor, no solo una opción marcada.
* **AC-A1.2** — La instrucción de sistema del motor se versiona en el repositorio y se revisa en PR como contenido doctrinal (G2.1).

## Bloque B — Retención y minimización

### HU-B1 — Guardar lo justo
Como usuario, quiero que mi pregunta no quede archivada con mi nombre.

* **AC-B1.1** — El **texto** de la pregunta se conserva solo lo necesario para el muestreo de revisión (G5.2) y con un plazo corto configurable; vencido, se borra por trabajo programado. Test: `test('AC-B1.1: el purgado elimina el texto vencido y conserva el agregado')`.
* **AC-B1.2** — El texto conservado va **desvinculado del usuario**: `ai_answer_log` guarda el hash de la pregunta y su contenido para revisión, y la contabilidad de cuota (`ai_quota_usage`) guarda usuario y contador, **nunca ambos juntos** (G6.2).
* **AC-B1.3** — Los agregados que sobreviven al purgado son anónimos y por tema (AC-C1.2 del spec 54).
* **AC-B1.4** — Borrado de cuenta: el flujo existente de desvinculación/borrado cubre también `ai_quota_usage`. Test.

## Bloque C — Documentos y consentimiento

* **AC-C1.1** — `legal/politica-de-privacidad.md` y su copia publicada (`apps/admin-cms/privacidad.html`) se actualizan con: la existencia de la función, el proveedor como **subencargado de tratamiento**, qué se envía (la pregunta y fragmentos del corpus), qué **no** se envía nunca (Diario, Reconciliación, intenciones privadas, ubicación, perfil), la retención y el derecho de supresión.
* **AC-C1.2** — Primer uso: pantalla de aceptación breve y clara —qué es, qué no es (no sustituye al sacerdote), qué se envía y qué no— con registro de aceptación, siguiendo el patrón de `donation_terms_acceptances` del R9.1.
* **AC-C1.3** — Revisión por el abogado antes de publicar, igual que el disclaimer de donaciones. **R6.2 no se lanza sin esta revisión.**
* **AC-C1.4** — Fichas de tienda (`docs/store-readiness.md`): Apple y Google exigen declarar el uso de IA generativa y la recogida de datos asociada; se actualizan las declaraciones de privacidad de ambas fichas y se revisa la clasificación por edad.

## Bloque D — Proveedor y presupuesto (decisión del dueño)

* **AC-D1.1** — Queda registrada la elección de proveedor de modelo y de *embeddings*, con: coste por 1.000 consultas estimado, latencia esperada, calidad en español, compromiso de no entrenamiento y región de tratamiento. La clave es secreto de servidor (patrón `ELEVENLABS_API_KEY`), nunca en el cliente.
* **AC-D1.2** — Queda registrado el **presupuesto mensual tope** y la conducta al agotarse: degradar a recuperación (AC-D1.2 del spec 52). Sin este valor configurado, el motor no genera (AC-D1.5 del spec 52).
* **AC-D1.3** — Se documenta la alternativa evaluada de *embeddings* propios/locales para R6.1 —que permitiría que la búsqueda semántica no dependa de ningún proveedor externo ni tenga coste variable— y la razón de la elección.

## Verificación

* Un test por AC con su identificador.
* **Test de fuga** (G6.1): recorre las rutas del motor y verifica que ninguna alcanza `journal_entry`, notas de Reconciliación ni `rosary_session.intention_note`; complementa a `journal_crypto_test.dart`.
* Revisión de la lista MASVS (`docs/security-masvs-checklist.md`) con la superficie nueva: almacenamiento, tráfico de red y secretos del servidor.
* Revisión legal firmada antes del lanzamiento de R6.2.
