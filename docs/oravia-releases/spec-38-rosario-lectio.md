# Spec 38 — Rosario y Lectio: pulido de oración

**Release:** 8 "Orar con hondura" · **Mejoras origen:** M1, M6 · **Esfuerzo:** S · **Orden:** 1
**Amplía:** spec 29 (Rosario) · **ADRs:** 005 (offline-first) · **Dependencias:** ninguna (el bloque B espera el dictamen D1)

## Contexto

El Rosario actual (`rosary_controller.dart`, secuencia lineal de 82 pasos en `_buildSequence()`) arranca directamente con la Señal de la Cruz: no hay saludo ni momento de intenciones — la intención es un `TextFormField` anclado al fondo del `ListView` de `rosary_screen.dart`, visible durante todos los pasos. Las 3 avemarías de apertura ya llevan intención por las virtudes teologales como texto en cursiva (`hailMaryVirtueIntention`, `rosary_content.dart:62-80`). El Padre Nuestro por las intenciones del Papa muestra en pantalla el placeholder literal `"[Fórmula tradicional — pendiente de dictamen editorial.]"` (`rosary_content.dart:251` es, `:356` en); hoy solo se limpia para el audio.

Lectio Divina (`lectio_divina_screen.dart`) muestra únicamente la cita del Evangelio ("Evangelio · Jn 3, 16-21") aunque `todayContentProvider` ya trae `gospel.textBody`.

## Bloque A — Apertura del Rosario (M1a, M1b)

### HU-A1 — Saludo de introducción
Como orante, quiero un saludo de bienvenida al iniciar el Rosario que me disponga a la oración.

* **AC-A1.1** — `rosary_content.dart` añade un paso tipo `greeting` (es/en, paridad i18n) como primer paso de la secuencia, antes de la Señal de la Cruz. Los tests de conteo/orden de pasos se actualizan por identificador de paso, no por índice mágico.
* **AC-A1.2** — El saludo se narra por el pipeline de audio existente (`generate-audio` + caché en Storage, nueva clave de clip), sin marcadores editoriales en el texto.

### HU-A2 — Momento de intenciones
Como orante, quiero un momento explícito para pensar mis intenciones, con invitación a escribirlas.

* **AC-A2.1** — Nuevo paso "Intenciones" inmediatamente después del saludo. El `TextFormField` de intención se traslada del fondo del `ListView` (`rosary_screen.dart:428-437`) a este paso; deja de estar visible en el resto de pasos.
* **AC-A2.2** — Si el usuario escribió su intención, se muestra como recordatorio discreto al anunciar cada misterio. La intención se guarda con la sesión local (Drift `rosarySessions`) y **nunca sale del dispositivo** (cero cambios de red).
* **AC-A2.3** — El paso es saltable con un toque. Test de widget verifica presencia, escritura opcional y salto.

## Bloque B — Virtudes y fórmula del Papa (M1c, M1d)

### HU-B1 — Encabezado de virtud
Como orante, quiero que las 3 avemarías de apertura anuncien "Por un aumento de la virtud de la Fe / Esperanza / Caridad, rezamos:" como encabezado del paso.

* **AC-B1.1** — Se sustituye el texto en cursiva actual por un encabezado estructurado del paso (campo propio del modelo de paso, no concatenación en el cuerpo), es/en con paridad. El audio narra el encabezado y luego la avemaría.

### HU-B2 — Eliminar el placeholder editorial
Como orante, no quiero ver "[Fórmula tradicional — pendiente de dictamen editorial.]" en pantalla.

* **AC-B2.1** — `rosary_content.dart:251` (es) y `:356` (en) sustituyen el placeholder por la fórmula aprobada por el revisor teológico (**Decisión D1 — bloquea solo este bloque**). El sanitizado especial para audio (`strip()` con `RegExp(r'\s*\[[^\]]*\]')` en `rosary_screen.dart:86-87`) se elimina al quedar sin uso.
* **AC-B2.2** — Test: ningún paso del Rosario contiene `[` en su texto visible, en ambos idiomas.

## Bloque C — Evangelio en Lectio Divina (M6)

### HU-C1 — Texto del Evangelio expandible
Como orante, quiero leer el texto del Evangelio dentro de Lectio Divina, con expandir/contraer.

* **AC-C1.1** — `lectio_divina_screen.dart` consume `gospel.textBody` de `todayContentProvider` (ya disponible; **sin llamadas de red nuevas**) bajo un componente expandible junto a la cita.
* **AC-C1.2** — Colapsado por defecto con la cita visible; el estado expandido no persiste entre sesiones. Se mantiene la decisión previa de "sin audio" en Lectio.
* **AC-C1.3** — Si el día no tiene Evangelio cargado, se conserva el comportamiento actual (`lectioNoGospel`).

## Decisiones del usuario (2026-08-07)

* El bloque B espera el dictamen del revisor teológico sobre la fórmula del Padre Nuestro por el Papa (D1); los bloques A y C avanzan sin él.

## Verificación

* Un test por AC, nombrado con su identificador (p. ej. `test('AC-B2.2: ningún paso visible contiene "["')`).
* Paridad i18n de todas las claves nuevas; conteo total de pasos del Rosario actualizado y verificado.
* QA manual: rezo completo con audio en es y en, con y sin intención escrita.
