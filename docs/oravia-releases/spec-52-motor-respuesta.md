# Spec 52 — Motor de respuesta `ask-catholic`

**Release:** 6 · rama de recuperación en **R6.1**, rama de generación en **R6.2** · **Esfuerzo:** L
**ADRs:** 002 (Supabase) · 011 (nuevo) · **Dependencias:** spec 50 (corpus), spec 51 (guardrails), spec 55 (proveedor y presupuesto) · **Desbloquea:** spec 53

## Contexto

El proyecto ya tiene el patrón exacto que este motor necesita, y conviene copiarlo antes que reinventarlo. `supabase/functions/generate-audio/index.ts` demuestra la disciplina de la casa para un servicio externo de pago:

* la clave del proveedor es **secreto de servidor** (`ELEVENLABS_API_KEY`), nunca viaja al cliente;
* la función **solo** acepta entradas de una lista blanca (lecturas reales del calendario, `CLIP_ID` con forma conocida, texto acotado a 2600 caracteres);
* **caché compartida llaveada por hash del texto** — «solo el PRIMER oyente paga la síntesis», y el hash en la ruta evita el envenenamiento de la caché;
* la sesión de la app se verifica por JWT por defecto.

`ask-catholic` hereda las cuatro propiedades. La diferencia estructural es que una pregunta libre es texto nuevo por definición, así que la disciplina de coste tiene que ser más explícita (§ Bloque D).

## Bloque A — Recuperación (R6.1)

### HU-A1 — Encontrar lo que la app ya tiene
Como usuario, quiero preguntar con mis palabras y que la app me lleve al contenido que ya contiene.

* **AC-A1.1** — Edge Function `ask-catholic` (Deno), invocable solo con sesión válida de la app. Entrada `{ question, locale }`; `question` acotada (p. ej. 4–500 caracteres) y `locale` en `('es','en')`, con el mismo rigor de validación que `generate-audio`.
* **AC-A1.2** — Normaliza la pregunta (minúsculas, espacios, signos), calcula su hash y consulta en orden: (1) `ai_answer_bank` aprobado → devuelve sin coste; (2) caché de respuestas `ai_answer_cache` por hash + locale → devuelve sin coste; (3) recuperación en `corpus_chunk` por similitud vectorial filtrando por `locale`.
* **AC-A1.3** — Devuelve los k fragmentos con `title`, `citation`, `deep_link` y su extracto, ordenados por relevancia, más el `kind` para que la app agrupe por tipo de fuente.
* **AC-A1.4** — Si el mejor resultado no alcanza el umbral de similitud, devuelve `{ status: 'unknown' }` y la app muestra el mensaje de no-saber con derivación (G1). Test: `test('AC-A1.4: bajo umbral devuelve unknown')`.
* **AC-A1.5** — Los guardrails G3 (materias reservadas) y G6 (sin datos personales en el contexto) se aplican **también en R6.1**, antes de la recuperación.

## Bloque B — Generación fundamentada (R6.2)

### HU-B1 — Una síntesis catequética, no un montón de fragmentos
Como usuario, quiero una respuesta breve y clara, con sus fuentes debajo.

* **AC-B1.1** — El modelo recibe: la instrucción de sistema versionada (G2/G4), los fragmentos recuperados como **única** fuente de verdad, y la pregunta. Nada más. Test de construcción de contexto: `test('AC-B1.1: el contexto contiene solo instrucción, fragmentos y pregunta')`.
* **AC-B1.2** — La salida es estructurada: `answer` (síntesis breve), `citations` (subconjunto de los fragmentos realmente usados) y `confidence`. Una salida sin citas se descarta y se degrada a la respuesta de recuperación de R6.1 — **el fallo del modelo nunca produce una respuesta sin fuente**.
* **AC-B1.3** — La pregunta del usuario se trata como dato, no como instrucción: los intentos de inyección («ignora tus reglas», «actúa como sacerdote») no alteran el comportamiento. Cubierto por la suite de evaluación del spec 51.
* **AC-B1.4** — Toda respuesta generada se registra en `ai_answer_log` con: hash de la pregunta, fragmentos citados, versión del prompt, modelo, tokens y coste. Sin identificador de usuario junto al texto (G6.2). Es lo que hace posible el muestreo (G5.2) y la retirada (G5.3).

## Bloque C — Esquema y RLS

* **AC-C1.1** — Migración `0041_ai_engine.sql`: `ai_answer_bank` (pregunta canónica, respuesta aprobada, `review_id`, locale, `retired`), `ai_answer_cache` (hash, locale, payload, `expires_at`), `ai_answer_log` (ver AC-B1.4), `ai_reserved_topics` (spec 51), `ai_quota_usage` (usuario, día, contador).
* **AC-C1.2** — RLS desde el minuto uno (ADR-007): ninguna de estas tablas es legible por `anon`/`authenticated`; solo service role y roles editoriales. Un usuario **no puede leer las consultas de otro** ni las propias por vía directa. Suite `scripts/ai_engine_rls_test.sql`.
* **AC-C1.3** — `ai_answer_bank` se somete al circuito editorial existente: `review_id` → `content_review_status`, y una respuesta llega a `published` solo con firma de `revisor_teologico` (mismo trigger que el resto del contenido).

## Bloque D — Coste, cuotas y degradación

### HU-D1 — Que una función de pago no pueda sorprender a nadie
Como dueño del producto, quiero un tope duro que no dependa de que yo mire el panel.

* **AC-D1.1** — Cuota diaria por usuario configurable (`ai_quota_usage`), con mensaje amable al agotarse y sugerencia de explorar Catecismo/Biblia por su cuenta.
* **AC-D1.2** — **Tope global de gasto mensual configurable en servidor.** Al alcanzarlo, el motor **degrada automáticamente a R6.1** (solo recuperación y banco aprobado, coste ~0) en vez de apagarse: el usuario sigue encontrando contenido. Test: `test('AC-D1.2: con presupuesto agotado el motor responde en modo recuperación')`.
* **AC-D1.3** — Caché con TTL y banco de respuestas priorizados sobre la generación: una pregunta frecuente se paga **una vez**, igual que un clip de audio.
* **AC-D1.4** — Métricas por día en el CMS (spec 54): consultas, % servidas desde banco/caché, % `unknown`, coste acumulado y proyección de cierre de mes.
* **AC-D1.5** — Sin la variable de tope configurada, la función **no arranca en producción**: devuelve error de configuración en vez de gastar. Test: `test('AC-D1.5: sin tope configurado el motor no genera')`.

## Verificación

* Un test por AC con su identificador; suite `scripts/ai_engine_rls_test.sql` en CI.
* Tests de la Edge Function con el proveedor mockeado, siguiendo el patrón acordado para `send-push` (AC-A1.2 del spec 45).
* Prueba de carga acotada en staging: verificar que la caché y el banco absorben la repetición y que la cuota corta como se espera.
* La suite de evaluación doctrinal (spec 51) corre contra esta función, no contra un simulador.
