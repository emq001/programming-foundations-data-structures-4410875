# Spec 40 — Planes de Lectura 2.0

**Release:** 8 "Orar con hondura" · **Mejora origen:** M4 · **Esfuerzo:** L · **Orden:** 7
**Amplía:** spec 34 (Planes de lectura) · **ADRs:** 004 (cifrado local), 005 (offline-first) · **Dependencias:** ninguna (el audio reutiliza la Edge Function existente)

## Contexto

Hoy existen 4 planes bundled (`reading_plans.dart`: `evangelios-30`, `salmos-consuelo` 14, `semana-santa` 8, `historia-salvacion` 21) con progreso por checkbox en Drift (`readingPlanProgress`) y una barra simple. No hay: noción de "día actual" (no se guarda cuándo se empezó), audio, reflexión posterior, estadísticas destacadas ni recordatorio por plan. Al terminar de leer un pasaje el usuario queda en la pantalla de la Biblia, sin retorno guiado al plan.

## Bloque A — El día de hoy y navegación de retorno

### HU-A1 — Resaltar la lectura que me toca
Como lector, quiero que el plan resalte automáticamente la lectura de hoy en amarillo/acento.

* **AC-A1.1** — Migración local Drift añade `startedAt` al progreso del plan. "Día actual" = mínimo entre (primer día no completado) y (días transcurridos desde `startedAt`). El día actual se resalta visualmente y la lista hace auto-scroll hasta él al abrir el detalle del plan.

### HU-A2 — Volver al avance al terminar
Como lector, al terminar de leer (o escuchar) quiero volver a la vista de avance del plan con una confirmación visual.

* **AC-A2.1** — Al marcar el día como completado desde la lectura, se navega de regreso al detalle del plan con animación del progreso y refuerzo textual ("Día 12 de 30 completado").
* **AC-A2.2** — Los pasajes abiertos desde un plan llevan contexto de origen (parámetro de ruta), para que el botón de completar/volver aparezca solo en ese caso.

## Bloque B — Audio de lecturas

### HU-B1 — Escuchar la lectura del día
Como lector, quiero escuchar la lectura del día con las voces de la app.

* **AC-B1.1** — Reutiliza la Edge Function `generate-audio` + caché compartida en Storage, con clave `plan/día/idioma/voz`; reproducción con el `PrayerAudioService`/`just_audio` existentes. Los textos bíblicos son bundled con licencia ya declarada.
* **AC-B1.2** — Comportamiento offline (ADR-005): si no hay audio cacheado y no hay red, el botón se degrada con mensaje claro sin bloquear la lectura.

## Bloque C — Reflexión personal

### HU-C1 — "¿Qué me dijo Dios hoy?"
Como lector, al completar el día quiero anotar una breve reflexión privada.

* **AC-C1.1** — Campo opcional al marcar el día; se guarda **cifrado con `JournalCrypto` (AES-256-GCM)** en una tabla Drift nueva; **nunca viaja al servidor** (Decisión D8).
* **AC-C1.2** — Las reflexiones se releen desde el historial del plan (descifrado solo en memoria).

## Bloque D — Estadísticas con números grandes

### HU-D1 — Ver mi avance de un vistazo
Como lector, quiero ver mi avance con números grandes: días completados, racha del plan y porcentaje.

* **AC-D1.1** — Cabecera del detalle del plan con tipografía display (Lora) y tokens de `AppColors`; datos derivados del progreso Drift existente, sin red. La racha del plan se calcula de las fechas de completado.

## Bloque E — Recordatorio diario por plan

### HU-E1 — Programar mi recordatorio
Como lector, quiero programar un recordatorio diario del plan a la hora que elija.

* **AC-E1.1** — `ReminderScheduler` gana el canal **1300+** (un id por plan activo), con deep link a la ruta del plan; opt-in con selector de hora, cancelación automática al completar o abandonar el plan. Test de programación/cancelación.
* **AC-E1.2** — Los textos de la notificación pasan por l10n (es/en), corrigiendo el patrón actual de textos hardcodeados en `reminder_scheduler.dart`.

## Bloque F — Más planes

### HU-F1 — Nuevos planes de lectura
Como lector, quiero más planes para elegir.

* **AC-F1.1** — 4 planes nuevos bundled en `reading_plans.dart` (propuesta editorial: Hechos de los Apóstoles 28 días, Evangelio de Marcos 16, María en la Escritura 9, Adviento 24), es/en con paridad, revisión editorial previa con la misma fuente bíblica licenciada.

## Decisiones del usuario (2026-08-07)

* La reflexión personal se cifra localmente (D8).
* Los recordatorios son locales (no push remoto).

## Verificación

* Un test por AC con su identificador; tests de cálculo de "día actual" y de racha con fechas simuladas.
* Test de cifrado: la reflexión no aparece en claro en la base local ni en tráfico de red.
* QA manual del ciclo completo: empezar plan → leer/escuchar → completar → reflexión → estadísticas → recordatorio.
