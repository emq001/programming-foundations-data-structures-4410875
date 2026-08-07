# Spec 42 — Recursos y Catecismo ampliados

**Release:** 8 "Orar con hondura" · **Mejoras origen:** M2, M5 · **Esfuerzo:** M · **Orden:** 5
**Amplía:** spec 35 (Catecismo) · **ADRs:** — · **Dependencias internas:** el bloque A desbloquea el B

## Contexto

Recursos Externos (`external_resource.dart`, migración `0017_external_resources.sql`) solo admite `platform in ('youtube','facebook','instagram','tiktok')` — **no existe el tipo "Web"**, que es exactamente el problema que el dueño del producto encontró al intentar agregar encíclicas. Tampoco hay categorías: la lista es plana por `created_at`. El Catecismo interno (`catechism_content.dart`) tiene 4 pilares con 25 preguntas/respuestas de redacción propia (no reproduce el CIC por derechos de LEV) y no soporta imágenes.

## Bloque A — Tipo "Web" y categorías (M2, incluye el fix del bug)

### HU-A1 — Recursos de tipo página web
Como usuario (y como editor), quiero poder agregar y ver recursos que son páginas web, no solo videos o redes sociales.

* **AC-A1.1** — Migración nueva (0034+) amplía el CHECK de `platform` con `'web'`; `ResourcePlatform` gana `web` con ícono propio; `resource_player_screen.dart` abre los recursos web en el `webview_flutter` existente (con opción "abrir en el navegador").
* **AC-A1.2** — El CMS (`apps/admin-cms/index.html`) ofrece "Web" en el selector de plataforma. Fix directo del bug reportado.

### HU-A2 — Clasificación por categorías
Como usuario, quiero filtrar los recursos por categoría para encontrar lo que busco.

* **AC-A2.1** — Columna `category` con CHECK (`'enciclicas'`, `'catecismo'`, `'video'`, `'musica'`, `'formacion'`, `'oracion'`, `'noticias'`, `'general'`) + índice; chips de filtro en `external_resources_screen.dart` (hoy lista plana); campo de categoría en el CMS.
* **AC-A2.2** — Recursos existentes sin categoría caen en "General"; las políticas RLS no cambian; la cola de moderación `pending` sigue igual.

## Bloque B — Encíclicas y catecismos oficiales (M2 + M5b)

### HU-B1 — Encíclicas al alcance
Como usuario, quiero acceder a las encíclicas principales desde Recursos Externos.

* **AC-B1.1** — Seed editorial de encíclicas como recursos tipo `web`, categoría `enciclicas`, **enlazando a vatican.va** (Decisión del usuario: enlaces oficiales, sin embeber texto → sin riesgo de licencia). Metadatos en la descripción: papa y año.

### HU-B2 — Catecismo oficial completo (adultos y jóvenes)
Como usuario, quiero llegar al Catecismo de la Iglesia Católica completo y a la versión para jóvenes desde la app.

* **AC-B2.1** — Entradas categoría `catecismo` con enlaces a las fuentes oficiales (CIC completo en vatican.va; Youcat solo enlace a su sitio oficial, por ser editorial privada).
* **AC-B2.2** — El Catecismo interno muestra un acceso "Ir a la fuente completa" que lleva a esta categoría de Recursos.

## Bloque C — Completar el Catecismo interno (M5a)

### HU-C1 — Secciones más completas
Como usuario, quiero que la versión resumida del Catecismo cubra más temas.

* **AC-C1.1** — `catechism_content.dart` crece de 25 a ~60 preguntas/respuestas de **redacción propia** (nunca texto literal del CIC), añadiendo referencias numéricas al CIC en cada entrada ("cf. CIC 1210-1211"). La revisión del revisor teológico queda documentada en el PR (contenido bundled: la gobernanza de `enforce_review_transition` no aplica, la revisión es previa al merge).
* **AC-C1.2** — Propuesta de ampliación por pilar: Credo +10 (Trinidad, Iglesia, María, escatología), Sacramentos +8 (uno por sacramento), Vida en Cristo +10 (mandamientos, virtudes, doctrina social básica), Oración +7 (Padre Nuestro por peticiones).

## Bloque D — Imágenes por tema (M5c)

### HU-D1 — Imagen por pilar/tema
Como usuario, quiero una imagen o motivo visual que represente el tema que estoy viendo.

* **AC-D1.1** — `CatechismEntry`/pilar gana `artAsset` opcional resuelto vía `section_visuals.dart` (única fuente de verdad visual): motivos `CustomPainter` existentes y/o arte de dominio público en `assets/art/*.webp` con crédito en `docs/art-credits.md`. El test de contraste AA cubre los usos nuevos.

## Decisiones del usuario (2026-08-07)

* Encíclicas y catecismos oficiales van como **enlaces oficiales** (vatican.va / sitios oficiales), no texto embebido.

## Verificación

* Un test por AC con su identificador; test de la migración (CHECK acepta `web` y las categorías; rechaza valores fuera de la lista).
* Suite RLS `external_resources_rls_test.sql` extendida a la columna nueva.
* Test existente de 4 pilares es/en actualizado al nuevo conteo de entradas; paridad i18n.
