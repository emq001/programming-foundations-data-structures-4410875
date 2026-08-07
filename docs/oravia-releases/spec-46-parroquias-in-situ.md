# Spec 46 — Registro de parroquias in situ y fotos

**Release:** 9 "Iglesia viva" · **Mejora origen:** M13 · **Esfuerzo:** M · **Orden:** 1 en R9
**Amplía:** spec 30 (Parroquias) · **ADRs:** 003 (cuenta), nota D4 sobre GPS · **Funda:** bucket `parish-media` + strip EXIF + cola CMS (reutilizados por specs 47 y 49)

## Contexto

El mapa de parroquias (`parish_map_screen.dart`, OpenStreetMap con `flutter_map` + `geolocator`) permite buscar, confirmar vigencia, reportar y reclamar administración — pero **no permite dar de alta una parroquia desde la app** ni subir fotos (`Parish` no tiene campo de imagen; `image_picker` está en `pubspec.yaml` pero solo lo usa el feedback). Este spec habilita el registro in situ con aprobación editorial.

## Bloque A — Alta desde el lugar

### HU-A1 — Registrar la iglesia donde estoy
Como usuario parado frente a una iglesia que no está en el listado, quiero darla de alta capturando su ubicación con un botón.

* **AC-A1.1** — Botón "Registrar esta iglesia" en `parish_map_screen.dart`. Con la ubicación del móvil habilitada, `geolocator` (ya en uso) captura lat/lng; formulario con nombre (obligatorio), dirección, ciudad/país (pre-llenados por la posición si es posible) y horarios opcionales. Se crea una fila en la tabla nueva `parish_submissions` con status `pending` — **no toca `parishes` hasta la aprobación**.
* **AC-A1.2** — Excepción documentada a la regla de GPS (Decisión D4): lo que viaja es la **posición del templo**, enviada por acción explícita y consentida del usuario (texto de consentimiento en el formulario) — no telemetría del usuario. Registrar como nota de ADR.
* **AC-A1.3** — Dedupe: si existe una parroquia aprobada a menos de ~100 m, se muestra antes de enviar ("¿Es esta?") para evitar duplicados.
* **AC-A1.4** — Enviar un alta requiere cuenta vinculada (ADR-003, mismo criterio que reclamar administración).

## Bloque B — Fotos

### HU-B1 — Adjuntar fotos de la iglesia
Como usuario, quiero subir 2-3 fotografías de la iglesia.

* **AC-B1.1** — `image_picker` sube a un bucket de Storage nuevo `parish-media` (siguiendo el patrón de buckets `stories`/`feedback`): compresión previa y **strip de EXIF/geodatos** antes de subir; máximo 3 fotos y tamaño máximo por archivo.
* **AC-B1.2** — Las fotos de un envío `pending` solo son visibles para el autor y el equipo editorial (políticas de Storage), hasta la aprobación.

## Bloque C — Aprobación y publicación

### HU-C1 — Aprobar o rechazar altas
Como editor, quiero revisar las altas de parroquia desde el CMS antes de que se publiquen.

* **AC-C1.1** — Cola en el CMS reutilizando el patrón de verificación editorial manual del "reclamar administración" (spec 37, AC-A1.3). Al aprobar: se crea la fila en `parishes` (que gana columna `photo_urls`) y las fotos pasan a la ficha (`parish_detail_screen.dart` muestra una galería sencilla). Al rechazar: se notifica al autor con motivo (push transaccional vía spec 45 o aviso in-app).
* **AC-C1.2** — El autor ve el estado de su envío ("en revisión / aprobada / rechazada") en su perfil, patrón `myPendingCount()` de Recursos Externos.

## Decisiones del usuario (2026-08-07)

* **Sí hay proceso de aprobación** (pregunta abierta del pedido original): toda alta pasa por revisión editorial antes de publicarse, coherente con la gobernanza existente de parroquias.

## Verificación

* Un test por AC con su identificador; suite RLS/SQL de `parish_submissions` y de las políticas del bucket `parish-media` (el autor no puede aprobar su propio envío; nadie lee envíos ajenos pendientes).
* Test unitario del strip de EXIF (imagen con geodatos entra → archivo subido sin metadatos).
* QA manual: alta completa in situ con fotos en Android e iOS, aprobación en CMS, aparición en el mapa.
