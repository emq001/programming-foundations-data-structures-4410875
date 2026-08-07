# Spec 41 — Retos Espirituales v2

**Release:** 8 "Orar con hondura" · **Mejora origen:** M9 · **Esfuerzo:** M · **Orden:** 6
**ADRs:** 005 (offline-first) · **Dependencias:** sistema de puntos existente (spec 33)

## Contexto

Los Retos Espirituales actuales (`challenges_screen.dart`) son una lista estática de 4 retos (`gratitud-7`, `silencio-7`, `projimo-5`, `examen-7`) cuyo único estado es un `Set<String>` dentro del `State` del widget: **se pierde al salir de la pantalla**. "Marcar hoy" solo alimenta la racha global. El propio código lo reconoce: "el seguimiento día-por-día de cada reto llega en v2" (`challenge.dart:4`). Esta es esa v2.

## Bloque A — Aceptar el reto y persistir

### HU-A1 — Aceptar un reto
Como usuario, quiero aceptar un reto y que mi progreso no se pierda al salir de la pantalla.

* **AC-A1.1** — Tabla Drift `challenge_progress` (`challengeId`, `acceptedAt`, día, `completedAt`) reemplaza el `Set<String>` efímero; migración local incluida. Un reto aceptado aparece como "en curso" en la lista.
* **AC-A1.2** — Solo un intento activo por reto; abandonar pide confirmación y conserva el historial de intentos.

## Bloque B — Seguimiento día a día y avance

### HU-B1 — Marcar cada día y ver mi avance
Como usuario, quiero marcar cada día del reto y ver cuánto llevo.

* **AC-B1.1** — Los 4 retos se re-modelan con estructura día-a-día (duración, texto/práctica por día). Vista de detalle con checks diarios, barra de avance y "día N de M" con números grandes (mismo lenguaje visual del spec 40-D).
* **AC-B1.2** — **Los retos otorgan puntos por día completado** (Decisión del usuario): la concesión pasa por el RPC de puntos con `point_rule` y tope diario **validados en servidor**, nunca calculados en cliente. Propuesta: 1 día de reto puntuable por día natural.
* **AC-B1.3** — Completar el reto entero muestra una celebración y lo registra en el historial.

## Bloque C — Recordatorios

### HU-C1 — Recordatorio del reto
Como usuario, al aceptar un reto quiero elegir una hora de recordatorio diario.

* **AC-C1.1** — Canal **1400+** en `ReminderScheduler` (un id por reto activo), deep link al detalle del reto; se cancela automáticamente al completar o abandonar. Textos por l10n es/en.

## Bloque D — Más retos

### HU-D1 — Nuevos retos
Como usuario, quiero más retos para distintos momentos espirituales.

* **AC-D1.1** — Nuevos retos bundled con revisión editorial (propuesta: 7 días de gratitud ampliado, 33 días de consagración, 5 días de silencio, reto semanal de caridad, reto de perdón); es/en con paridad; formato día-a-día del bloque B.

## Decisiones del usuario (2026-08-07)

* Los retos **sí** otorgan puntos de constancia, con tope diario en servidor.

## Verificación

* Un test por AC con su identificador; test clave: el progreso sobrevive a cerrar y reabrir la app (persistencia Drift).
* Test RLS/SQL de la regla de puntos nueva (tope diario) en `scripts/points_groups_rls_test.sql` o suite equivalente.
* Paridad i18n de claves nuevas.
