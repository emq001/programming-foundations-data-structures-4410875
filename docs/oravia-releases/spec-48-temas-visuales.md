# Spec 48 — Temas visuales

**Release:** 9 "Iglesia viva" (bloque A0 se adelanta a R8) · **Mejora origen:** M14 · **Esfuerzo:** L · **Orden:** 3 en R9 (paralelizable)
**ADRs:** requiere **ADR-010** que enmiende ADR-009 (identidad única "Camino") · **Dependencias:** ninguna

## Contexto

La app tiene una sola identidad visual ("Camino": tokens `AppColors` Light/Dark en `app_theme.dart`, Lora + Karla, color litúrgico como `tertiary`) y una regla de la casa: `section_visuals.dart` es la única fuente de verdad de colores por sección, con contraste AA verificado por test. Además `main.dart:66-67` fija `ThemeMode.system` sin selector. Respuesta a la pregunta del dueño ("¿muy difícil?"): **moderado, no trivial** — el sistema de tokens existente lo hace factible sin reescribir pantallas, pero exige diseñar 2 paletas nuevas completas (claro+oscuro), una decisión de arquitectura (ADR-010) y QA visual amplio. Por eso: selector claro/oscuro en R8 (barato) y variantes completas en R9.

## Bloque A0 — Selector claro/oscuro/sistema (se adelanta a R8)

### HU-A0.1 — Elegir modo de apariencia
Como usuario, quiero elegir claro, oscuro o automático desde "Yo".

* **AC-A0.1** — Selector en Ajustes de `profile_screen.dart`; `ThemeMode` persistido en prefs (patrón `reading_prefs.dart`) y aplicado en `main.dart` en lugar del valor fijo.

## Bloque A — Arquitectura de variantes (R9)

### HU-A1 — Elegir el diseño de mi app
Como usuario, quiero elegir entre 2-3 diseños visuales de la app desde "Yo".

* **AC-A1.1** — `app_theme.dart` se parametriza con `ThemeVariant { camino, joven, sereno }`; **`section_visuals.dart` sigue siendo la única fuente de verdad**, resolviendo sus tokens por variante (la regla de la casa se preserva; las pantallas no cambian). Formalizado en ADR-010.
* **AC-A1.2** — Selector de variante en `profile_screen.dart` con vista previa; persistido en prefs; cambio en caliente sin reiniciar.
* **AC-A1.3** — El color litúrgico como `tertiary` se mantiene en todas las variantes (es identidad de producto, no de tema).

## Bloque B — Diseño de las variantes

### HU-B1 — Variante "Joven" y variante "Serena"
Como usuario joven quiero una variante más viva; como usuario que prefiere sobriedad, una más serena/informal.

* **AC-B1.1** — Dirección de arte documentada por variante: paleta completa Light+Dark (tokens equivalentes a `AppColors`), tipografía display/cuerpo propia (p. ej. Joven: colores más saturados y tipografía redondeada; Serena: neutros cálidos y tipografía humanista), y adaptación de los 23 motivos/gradientes de sección.
* **AC-B1.2** — Toda combinación variante × modo pasa el test de contraste AA existente (`section_visuals_contrast_test.dart` ejecutado por variante: 6 combinaciones como mínimo).

## Bloque C — QA visual

### HU-C1 — Congelar regresiones
Como equipo, queremos detectar regresiones visuales al tocar los temas.

* **AC-C1.1** — Golden tests por variante y modo en pantallas clave (Hoy, Rosario, Yo, Explorar); corren en CI.

## Decisiones del usuario (2026-08-07)

* **Variantes completas** (no solo acentos) en R9; el selector claro/oscuro/sistema se adelanta a R8.

## Verificación

* Un test por AC con su identificador; los tests de contraste y golden son el corazón de la verificación.
* QA manual: cambiar variante y modo en caliente recorriendo las 5 tabs; verificar persistencia tras reinicio.
