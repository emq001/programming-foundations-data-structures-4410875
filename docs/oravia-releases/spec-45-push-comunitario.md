# Spec 45 — Push comunitario

**Release:** 8 "Orar con hondura" · **Mejora origen:** M10 · **Esfuerzo:** M · **Orden:** 9 (cierra R8)
**ADRs:** 002 (Supabase) · **Dependencias:** ninguna · **Desbloquea:** spec 47-D (difusión de eventos en R9)

## Contexto

La app ya registra tokens FCM en `device_push_tokens` (migración 0030) y maneja push en foreground/background/cold start con deep link por `data['route']` (`push_service.dart`). Pero el **envío** sigue siendo manual con `scripts/send_push.py` y una cuenta de servicio fuera del repo — el propio código lo marca como pendiente. Este spec completa el circuito: envío server-side, panel en el CMS y preferencias del usuario.

## Bloque A — Envío server-side

### HU-A1 — Enviar sin el script manual
Como equipo, queremos enviar push a toda la comunidad OraVia desde infraestructura propia.

* **AC-A1.1** — Edge Function `send-push` (Deno) usando la cuenta de servicio FCM (secreto de servidor, patrón `ELEVENLABS_API_KEY`): lee `device_push_tokens`, envía en lotes con `android.priority=HIGH` (lección documentada del proyecto), y elimina tokens inválidos (respuesta UNREGISTERED). Reemplaza `scripts/send_push.py`.
* **AC-A1.2** — Invocable únicamente con service role / sesión con rol admin del CMS (verificación en la función); test de la función con FCM mockeado.

## Bloque B — Panel en el CMS y tipos de mensaje

### HU-B1 — Redactar y enviar campañas
Como editor, quiero redactar y enviar campañas desde el CMS, con tipo, deep link y ambos idiomas.

* **AC-B1.1** — Tabla `push_campaigns`: tipo con CHECK (`'anuncio'`, `'fiesta_liturgica'`, `'contenido_nuevo'`, `'oracion_urgente'`; en R9 se añade `'evento'`), título/cuerpo es+en, ruta de deep link, estado `draft → sent`, y **auditoría** (quién y cuándo envió). Panel de redacción/envío en `apps/admin-cms/index.html`.
* **AC-B1.2** — El deep link reutiliza el manejo existente del canal 1200 (`remote_push`): tocar la notificación navega a la ruta del payload.

**Tipos de mensaje propuestos** (respuesta al "descubre qué tipos"): anuncios de la comunidad (novedades de la app, hitos), fiestas litúrgicas y solemnidades (víspera), contenido nuevo (historias, cursos, planes), invitaciones a la oración en momentos señalados ("oración urgente": intenciones graves, catástrofes, peticiones del Papa), y —desde R9— eventos católicos destacados.

## Bloque C — Preferencias del usuario

### HU-C1 — Elegir qué recibo
Como usuario, quiero decidir qué tipos de push recibo.

* **AC-C1.1** — Opt-out por tipo en Ajustes de "Yo" (patrón visual de los canales de recordatorio); la segmentación usa el tipo + idioma asociado al token. El registro del token sigue siendo opt-in como hoy.
* **AC-C1.2** — Política anti-spam (Decisión D9): tope de frecuencia semanal por tipo y quiet hours configuradas en el servidor; **"oración urgente" no salta el opt-out** del usuario.

## Decisiones del usuario (2026-08-07)

* Este spec cierra R8 deliberadamente: es infraestructura cuyo valor pleno se cobra en R9 (eventos), pero salda una deuda pendiente desde R4/R5.

## Verificación

* Un test por AC con su identificador; suite RLS de `push_campaigns` (solo roles editoriales escriben; nadie lee tokens ajenos).
* Prueba end-to-end en staging: campaña desde el CMS → notificación en dispositivo Android e iOS → deep link correcto.
* Verificar limpieza de tokens UNREGISTERED con tokens caducados de prueba.
