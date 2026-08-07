# Spec 39 — Favoritos universales

**Release:** 8 "Orar con hondura" · **Mejora origen:** M3 · **Esfuerzo:** M · **Orden:** 3
**ADRs:** 005 (offline-first) · **Dependencias:** ninguna

## Contexto

El sistema de favoritos (`favorites_repository.dart`, local-first con sync best-effort a la tabla `favorite` de Supabase, clave `(contentType, contentId)`) hoy solo soporta dos tipos: `'section'` (rutas del catálogo, marcadas desde `ExploreScreen`) y `'daily_reading'` (únicamente el Evangelio del día, desde `gospel_screen.dart`). Las pantallas de Reflexión del Día (`reflection_screen.dart`), Oración del Día (`prayer_session_screen.dart`) y Santo del Día (`saint_of_day_screen.dart`) no tienen botón de favorito, ni tampoco Lectio, Catecismo, Formación ni versículos.

## Bloque A — Nuevos tipos de favorito

### HU-A1 — Favorito en el contenido diario
Como usuario, quiero marcar como favorito la Reflexión del Día, la Oración del Día y el Santo del Día para volver a ellos cuando quiera.

* **AC-A1.1** — `favorites_repository.dart` amplía `contentType` con `'reflection'`, `'prayer'` y `'saint'`. La clave (`contentId`) combina tipo + fecha o slug, de modo que cada favorito tenga un deep link de retorno válido en go_router.
* **AC-A1.2** — Se crea el widget reutilizable `FavoriteToggleButton` (ícono corazón, estado reactivo vía `favorites_controller.dart`) y se inserta en el `AppBar` de `reflection_screen.dart`, `prayer_session_screen.dart` y `saint_of_day_screen.dart`.
* **AC-A1.3** — El toggle funciona offline (Drift primero) y sincroniza a Supabase en best-effort, igual que los favoritos actuales.

### HU-A2 — Favorito en el resto de contenidos
Como usuario, quiero marcar como favorito entradas del Catecismo, lecciones de Formación, la Lectio del día y pasajes de los planes de lectura.

* **AC-A2.1** — Mismos widget y esquema; cada tipo nuevo define su `contentType` y su deep link para reabrir el contenido exacto desde Favoritos.
* **AC-A2.2** — La migración de rutas legacy existente (`migrateLegacyRoutes()`) no se rompe: test de regresión sobre favoritos previos.

## Bloque B — Vista de favoritos agrupada

### HU-B1 — Favoritos organizados en "Yo"
Como usuario, quiero ver mis favoritos agrupados por tipo y poder quitarlos fácilmente.

* **AC-B1.1** — La vista de favoritos (rail en `profile_screen.dart` y en `explore_screen.dart`) agrupa por `contentType` con encabezados; deslizar (swipe) quita el favorito con opción de deshacer.
* **AC-B1.2** — Tocar un favorito navega a su deep link; si el contenido ya no existe (p. ej. contenido diario antiguo), se muestra un estado vacío amable en vez de error.
* **AC-B1.3** — El texto de vacío `favoritesEmpty` se actualiza para reflejar que ahora casi todo se puede marcar.

## Decisiones del usuario (2026-08-07)

* Alcance: "cualquier otra funcionalidad que no lo tenga" se interpreta como todo contenido re-visitable (diario, Catecismo, Formación, Lectio, pasajes); las pantallas de flujo (p. ej. examen de Reconciliación) quedan fuera por diseño.

## Verificación

* Un test por AC con su identificador; tests de widget del `FavoriteToggleButton` en las tres pantallas del bloque A.
* Test de sincronización: favorito creado offline aparece en la tabla remota al reconectar (patrón de tests existente del repositorio).
* Paridad i18n de claves nuevas.
