# Spec 50 — Corpus curado: dictamen, licencias e ingesta

**Release:** 6 "IA católica confiable" (R6.1) · **Esfuerzo:** XL · **Orden:** 1 (gate de todo el release)
**ADRs:** 002 (Supabase) · 007 (RLS desde la migración 1) · **Dependencias:** dictamen teológico del contenido propio (compartido con el 4% pendiente del R4) · **Desbloquea:** specs 52, 53, 54

## Contexto

El proyecto ya tiene un mecanismo de gobernanza doctrinal que funciona y que este spec **reutiliza en lugar de inventar**: `content_review_status` con el enum `review_status ('draft', 'in_review', 'theological_review', 'approved', 'published', 'retired')`, el rol `revisor_teologico` en `editorial_role`, y el trigger `enforce_review_transition` (migración 0001) que hace `approved` inalcanzable sin `reviewed_by` de un revisor teológico y sin `license_type` declarado en la fuente.

Lo que falta no es el mecanismo: es haberlo aplicado al contenido empaquetado en la app. Hoy sigue marcado como no apto para publicación:

| Fuente | Volumen | Marca actual |
|---|---|---|
| `catechism_content.dart` | 120 entradas, 8 partes, 4 pilares | «PENDIENTE DE DICTAMEN TEOLÓGICO (#23): no apto para beta pública» |
| `formation_courses.dart` | cursos y rutas | misma marca |
| `reconciliation_content.dart` | guía y examen | misma marca |
| `family_content.dart` | guías de familia | «[Redacción propia — pendiente de dictamen teológico.]» inline |
| `night_reflection_content.dart`, `advent_content.dart`, `daily_phrases.dart` | reflexiones y frases | misma marca |
| Biblia `assets/bible/{es,en}` | 66 libros, 9.1 MB | Torres Amat 1825 (dominio público), «provisional y pendiente de dictamen»; deuterocanónicos en preparación |
| `external_resources` (migración 0034) | encíclicas y fuentes oficiales | enlaces `web` a vatican.va — **no se ingiere texto** |

## Bloque A — Dictamen y saneamiento (trabajo humano)

### HU-A1 — Corpus con firma
Como revisor teológico, quiero que cada pieza que la IA puede citar tenga mi firma y su licencia, para responder de lo que la app afirma.

* **AC-A1.1** — Todo el contenido empaquetado citable pasa a `content_review_status` con estado `published`, `reviewed_by` de un usuario con rol `revisor_teologico` y `source_reference.license_type` no nulo. Al terminar, **ninguna** de las marcas «pendiente de dictamen» sobrevive en `lib/data/bundled/` para el contenido incluido en el corpus. Test: `test('AC-A1.1: ningún fichero del corpus conserva la marca de dictamen pendiente')` sobre los ficheros declarados en el manifiesto de ingesta.
* **AC-A1.2** — Lo que no obtenga dictamen se marca `retired` o se excluye explícitamente del manifiesto. **No existe la categoría «pendiente» dentro del corpus**: o está aprobado o no entra.
* **AC-A1.3** — El dictamen del Catecismo propio conserva la regla vigente: referencias numéricas al CIC (`cicRef`), **nunca texto literal** (derechos LEV). Test que falla si un fragmento del corpus contiene texto atribuido al CIC.

### HU-A2 — Biblia utilizable y honesta
Como usuario, quiero saber qué traducción me está citando la IA.

* **AC-A2.1** — Los fragmentos bíblicos del corpus llevan la traducción en su procedencia (`Torres Amat, 1825, dominio público`) y la respuesta la muestra. Mientras los deuterocanónicos estén en preparación, una pregunta que solo se responda con ellos devuelve «no lo sé» (G1) en vez de una respuesta parcial silenciosa.
* **AC-A2.2** — Si en el futuro entra una traducción moderna con licencia, el cambio es de datos (nueva fuente + reingesta), no de código.

## Bloque B — Tabla de corpus y embeddings

### HU-B1 — Un corpus consultable con procedencia
Como ingeniería, queremos una tabla única de fragmentos con su origen, para que toda respuesta pueda citarse y auditarse.

* **AC-B1.1** — Migración `0040_corpus.sql`: extensión `vector` (pgvector) y tabla `corpus_chunk` con `id`, `kind` (`catecismo` | `biblia` | `oracion` | `formacion` | `santo` | `liturgia` | `recurso`), `locale` (`es` | `en`), `title`, `body`, `citation` (texto visible al usuario), `deep_link` (ruta interna a la pantalla que ya muestra ese contenido), `source_reference_id`, `review_id` → `content_review_status(id)`, `license_type`, `content_hash`, `embedding vector(N)`, `created_at`.
* **AC-B1.2** — **La ingesta rechaza** todo fragmento cuyo `review_id` no esté en `published` o cuya fuente no declare `license_type`, replicando la lógica del trigger editorial. Constraint + test SQL: `test('AC-B1.2: insertar un fragmento sin dictamen falla')`.
* **AC-B1.3** — RLS desde el minuto uno (ADR-007): `corpus_chunk` **no es legible por `anon` ni `authenticated`**. Solo la Edge Function (service role) y los roles editoriales leen. La app nunca consulta el corpus directamente. Suite `scripts/corpus_rls_test.sql` siguiendo el patrón de `editorial_rls_test.sql`.
* **AC-B1.4** — Índice de similitud (`ivfflat` o `hnsw`) sobre `embedding`, con filtro obligatorio por `locale`: una pregunta en español nunca recupera fragmentos en inglés.

### HU-B2 — Ingesta reproducible
Como ingeniería, queremos poder reconstruir el corpus entero desde cero y saber que salió igual.

* **AC-B2.1** — Script `scripts/ingest_corpus.dart` (o `tool/`): lee el manifiesto de fuentes, trocea por unidad natural —una entrada del Catecismo, un versículo con su contexto, un paso de una guía—, calcula `content_hash` y hace *upsert* idempotente. Ejecutarlo dos veces seguidas no cambia una sola fila. Test: `test('AC-B2.1: la ingesta es idempotente')`.
* **AC-B2.2** — El troceado **nunca parte una unidad doctrinal a la mitad**: la respuesta a una pregunta del Catecismo viaja completa con su pregunta. Test sobre las 120 entradas.
* **AC-B2.3** — Los *embeddings* se generan una sola vez por `content_hash` y se reutilizan; cambiar una palabra de un fragmento regenera solo ese. Coste de reingesta acotado y medible.
* **AC-B2.4** — El manifiesto declara qué queda **fuera** del corpus y por qué: Diario, notas de Reconciliación, `rosary_session.intention_note`, intenciones privadas, muro de intenciones, datos de parroquias y donaciones. Test: `test('AC-B2.4: el manifiesto excluye toda fuente personal')`.

## Bloque C — Cobertura y honestidad

### HU-C1 — Saber qué no sabe
Como equipo, queremos medir la cobertura real del corpus antes de exponerlo.

* **AC-C1.1** — Informe de cobertura por tema catequético (los 4 pilares) sobre un banco de 200 preguntas reales previstas; se publica el porcentaje con respuesta fundada. Un pilar por debajo del umbral acordado sale de la versión inicial en vez de responder mal.
* **AC-C1.2** — Las preguntas sin cobertura alimentan el backlog editorial en el CMS (spec 54): la IA no inventa contenido, lo **pide**.

## Verificación

* Un test por AC con su identificador; suite RLS `scripts/corpus_rls_test.sql` en CI.
* Test de licencias: ningún fragmento con `license_type` nulo o con texto del CIC.
* Test de privacidad: el manifiesto y la ingesta no pueden alcanzar tablas ni ficheros de contenido personal (ver también spec 55).
* La ingesta corre en CI contra un Supabase efímero y verifica el conteo esperado de fragmentos por `kind` y `locale`.
