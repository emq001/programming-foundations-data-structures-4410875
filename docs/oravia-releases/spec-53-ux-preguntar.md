# Spec 53 — «Preguntar» en la app

**Release:** 6 (R6.1, se enriquece en R6.2) · **Esfuerzo:** M
**ADRs:** 009/010 (identidad visual y variantes) · **Dependencias:** spec 52 · **Desbloquea:** —

## Contexto

La app tiene 67 pantallas y una regla visual firme: `lib/core/theme/section_visuals.dart` es la **única fuente de verdad** de color por sección, con contraste AA garantizado por test en las 6 combinaciones de variante × modo (ADR-010). Una sección nueva se añade ahí, no en la pantalla.

La entrada natural es **Explorar** (`explore_screen.dart`), que es donde el usuario ya busca contenido, más accesos contextuales desde Catecismo y Biblia — las dos pantallas donde la pregunta nace sola.

## Bloque A — La pantalla

### HU-A1 — Preguntar con mis palabras
Como usuario, quiero escribir una duda de fe y recibir una orientación con sus fuentes.

* **AC-A1.1** — Sección nueva «Preguntar» registrada en `section_visuals.dart` con su gradiente; el test de contraste AA existente la cubre en las 6 combinaciones sin excepciones.
* **AC-A1.2** — Entrada en Explorar y ruta propia en `go_router`. Campo de texto con el límite del motor (AC-A1.1 del spec 52) visible, y sugerencias de arranque tomadas del banco de respuestas aprobadas — que son, por definición, las que mejor se responden.
* **AC-A1.3** — La respuesta se presenta en dos partes claramente distintas: la orientación arriba y **las fuentes debajo, siempre visibles y tocables**. Tocar una fuente navega por `deep_link` a la pantalla de la app donde vive ese contenido (Catecismo, Biblia, guía, curso).
* **AC-A1.4** — Estado `unknown` (G1): mensaje honesto de que no hay respuesta fundada, con tres salidas concretas — Recursos Externos, buscar en el Catecismo, y hablar con el párroco (con la parroquia vinculada del usuario si la tiene).
* **AC-A1.5** — Aviso permanente y no descartable en la pantalla (G4.2): orientación catequética basada en el contenido de la app; no sustituye al sacerdote ni es acompañamiento espiritual. Redactado con el revisor teológico.

### HU-A2 — Preguntar desde donde surge la duda
Como usuario, quiero preguntar sin perder el hilo de lo que estoy leyendo.

* **AC-A2.1** — Acceso contextual desde Catecismo y desde el menú del versículo de la Biblia (el mismo menú que ya ofrece anotar y marcar, R4/v0.1.5), con la referencia precargada como contexto de la pregunta.
* **AC-A2.2** — La respuesta se puede guardar en **Favoritos** reutilizando el mecanismo universal del spec 39 (tabla `favorite`), sin inventar un almacén nuevo.

## Bloque B — Materias reservadas y crisis

* **AC-B1.1** — Cuando el motor devuelve derivación (G3), la pantalla muestra una tarjeta de acompañamiento —no un error—: reconoce la pregunta, explica por qué merece una persona y ofrece el camino (parroquia vinculada, horarios de confesión si están cargados, Recursos Externos).
* **AC-B1.2** — La tarjeta de crisis (G3.3) es texto fijo de `l10n`, nunca generado, con contactos de ayuda. Test: `test('AC-B1.2: la tarjeta de crisis no pasa por el motor')`.

## Bloque C — Offline, errores e i18n

* **AC-C1.1** — La app es offline-first (ADR-005) y esta pantalla no puede serlo: sin conexión muestra un estado claro y ofrece el contenido local (Catecismo, Biblia, guías) como alternativa. Se suma a `offline_screens_test.dart`.
* **AC-C1.2** — Errores del motor (cuota agotada, presupuesto agotado, proveedor caído) tienen mensajes distintos y accionables; el modo degradado (AC-D1.2 del spec 52) se percibe como «resultados encontrados», no como avería.
* **AC-C1.3** — Paridad i18n es/en completa para toda clave nueva (test de paridad existente); la pregunta se responde en el idioma del usuario y **solo** con fragmentos de ese `locale` (AC-B1.4 del spec 50).

## Verificación

* Un test por AC con su identificador.
* Test de contraste AA de la sección nueva en las 6 combinaciones variante × modo; golden del muestrario si procede (patrón spec 48).
* Test de paridad i18n es/en.
* QA en dispositivo: pregunta con respuesta, pregunta sin cobertura, materia reservada, sin conexión y con cuota agotada.
