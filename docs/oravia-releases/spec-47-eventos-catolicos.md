# Spec 47 — Eventos católicos cercanos

**Release:** 9 "Iglesia viva" · **Mejora origen:** M11 · **Esfuerzo:** XL · **Orden:** 2 en R9
**ADRs:** 007 (la tabla `event` ya existe reservada sin políticas), nota D4 sobre GPS · **Dependencias:** spec 46 (bucket `parish-media` + cola CMS), spec 45 (push, solo para el bloque D2)

## Contexto

No existe ninguna funcionalidad de eventos: lo más cercano es el calendario litúrgico y los horarios de parroquia como texto libre. La buena noticia: la tabla `event` ya está **reservada desde la migración inicial** (ADR-007, RLS sin políticas), esperando su release. Este es ese release.

## Bloque A — Modelo y gobernanza

### HU-A1 — Modelo de eventos seguro
Como equipo, queremos un modelo de eventos moderado y con los datos mínimos garantizados.

* **AC-A1.1** — Se materializa la tabla `event`: `title`, `description`, `starts_at`/`ends_at`, lugar (FK `parish_id` **o** dirección + lat/lng), `cost_type` CHECK (`'gratuito'`,`'ofrenda'`,`'costo'`) + monto opcional, `audience` CHECK (`'todos'`,`'jovenes'`,`'familias'`,`'ninos'`,`'adultos'`), `flyer_url`, `city`, `country`, `status` `pending → approved → rejected`, autor. RLS: lectura pública solo de `approved` con fecha futura; el autor lee/edita lo propio.
* **AC-A1.2** — **Datos obligatorios** (pedido explícito del dueño): día/horario, lugar, costo/ofrenda y público objetivo son NOT NULL; la validación se refleja también en el formulario.

### HU-A2 — Moderación con vía rápida parroquial
Como moderador, quiero aprobar eventos; como admin de parroquia verificado, quiero publicar sin fricción.

* **AC-A2.1** — (Decisión del usuario) Todo evento nace `pending` y lo aprueba el equipo editorial en el CMS, **salvo** los creados por administradores de parroquia verificados (`parish_admins`), que se aprueban de forma automática/exprés. Auditoría de quién aprobó.
* **AC-A2.2** — Reporte de evento por la comunidad (patrón `parish_reports`/`intention_reports`); un evento reportado N veces vuelve a revisión.

## Bloque B — Descubrir con filtros

### HU-B1 — Calendario y lista de eventos cercanos
Como usuario, quiero ver un calendario de eventos católicos con filtros por país, ciudad, parroquia, público, costo y fecha.

* **AC-B1.1** — La cercanía se calcula **en el dispositivo** (mismo patrón que parroquias: se descargan eventos por país/ciudad y se ordenan por distancia localmente); la posición del usuario **nunca viaja al servidor**.
* **AC-B1.2** — Vista calendario mensual + vista lista, con chips de filtro; ficha de evento con volante a pantalla completa al abrir, mapa (`flutter_map`) y acciones (recordar, compartir, reportar).
* **AC-B1.3** — Ruta propuesta `/explore/events`, tarjeta en el rail "Comunidad" de Explorar.

## Bloque C — Crear evento con volante

### HU-C1 — Publicar un evento
Como usuario, o como responsable autorizado de mi parroquia, quiero publicar un evento con su imagen tipo volante.

* **AC-C1.1** — Formulario con los campos obligatorios de AC-A1.2; el volante se sube al bucket `parish-media` (compartido con spec 46, mismo strip de EXIF y límites de tamaño); el evento nace `pending` (o aprobado si aplica la vía rápida A2.1).
* **AC-C1.2** — Publicar requiere cuenta vinculada (ADR-003). El autor ve el estado de sus eventos.

## Bloque D — Avisos

### HU-D1 — Recordatorio del evento
Como asistente, quiero un recordatorio del evento que me interesa.

* **AC-D1.1** — "Recordar" programa notificaciones locales vía `ReminderScheduler`, canal **1500+** (víspera y 2 horas antes), con deep link a la ficha; se cancelan si el evento se cancela o pasa.

### HU-D2 — Difusión de eventos destacados
Como editor, quiero difundir eventos destacados por push a la comunidad (o a un país/ciudad).

* **AC-D2.1** — Nuevo tipo de campaña `'evento'` en `push_campaigns` (spec 45), con segmentación básica por país si el token la tiene; respeta el opt-out por tipo.

## Decisiones del usuario (2026-08-07)

* Moderación: editorial + vía rápida para admins de parroquia verificados.
* Datos obligatorios: día/horario, lugar, costo/ofrenda, público objetivo.

## Verificación

* Un test por AC con su identificador; suite RLS/SQL de `event` (anónimo solo lee aprobados futuros; autor no aprueba lo suyo; vía rápida solo para `parish_admins` verificados).
* Test de filtros y de orden por distancia con posiciones simuladas (sin red).
* QA manual: ciclo completo usuario normal (pending → aprobación → visible) y admin parroquial (exprés), volante visible al abrir la ficha, recordatorios disparando.
