# OraVia — Plan maestro de Releases 8 y 9

**Fecha:** 2026-08-07 · **Estado:** Aprobado por el dueño del producto · **Base de código analizada:** `emq001/oravia` @ `7303e3a` (v0.1.5+8)

Este documento consolida el análisis de las 15 mejoras/funcionalidades solicitadas (M1–M15), su desarrollo como Features con historias de usuario (HU) y criterios de aceptación (AC), y el plan de incorporación en los próximos releases. Los detalles de cada Feature viven en los specs 38–49 de esta misma carpeta.

---

## 1. Numeración: el próximo release es el 8

Estado de la serie al corte del 7-ago-2026: R1–R3 al 100%, R4 al 96%, R5 al 75%, R5.1 al 88%, R6 (IA católica) al 0%, R7 (escalamiento institucional) al 50%. La serie no recicla números, por lo que el próximo release numerado es el **Release 8**. Dado el volumen y la naturaleza distinta de las 15 mejoras, el plan usa **dos releases**:

| Release | Nombre temático | Enfoque |
|---|---|---|
| **Release 8** | **"Orar con hondura"** | Pulido y profundización de funcionalidades existentes: alto valor, esfuerzo bajo-medio, sin gobernanza nueva. |
| **Release 9** | **"Iglesia viva"** | Funcionalidades nuevas de comunidad e Iglesia local: tablas nuevas, moderación, decisiones de seguridad. |
| **Release 9.1** | **"Manos abiertas"** | Piloto de donaciones solidarias vía parroquias (aislado por su riesgo legal/de seguridad, siguiendo el precedente de sub-releases R3.5–R3.11 y R5.1). |

Los specs continúan la serie existente (27–37) con los números **38–49**.

## 2. Trazabilidad: mejora solicitada → spec

| Mejora | Descripción corta | Spec | Release | Esfuerzo |
|---|---|---|---|---|
| M1 | Rosario: saludo, intenciones, virtudes, quitar "[Fórmula tradicional…]" | 38 (bloques A–B) | R8 | S |
| M6 | Lectio Divina: Evangelio expandible ahí mismo | 38 (bloque C) | R8 | S |
| M3 | Botón de Favorito en todas las funcionalidades | 39 | R8 | M |
| M4 | Planes de Lectura: audio, resaltar hoy, reflexión, estadísticas, recordatorio, más planes | 40 | R8 | L |
| M9 | Retos Espirituales: aceptar, seguimiento, avance, recordatorio, más retos | 41 | R8 | M |
| M2 | Encíclicas + categorías de recursos + fix tipo "Web" | 42 (bloques A–B) | R8 | M |
| M5 | Catecismo: completar secciones, catecismos oficiales, imágenes | 42 (bloques B–D) | R8 | M |
| M7 | Ranking Global: aviso de cuenta + podio top 3 | 43 (bloque A) | R8 | S |
| M8 | Formación: nuevas opciones (discovery) + progreso | 43 (bloque B) | R8 | S |
| M15 | Reconciliación: anotar faltas al momento (cifrado) | 44 | R8 | S |
| M10 | Push notifications a toda la comunidad | 45 | R8 | M |
| M13 | Registrar iglesia/parroquia in situ con fotos | 46 | R9 | M |
| M11 | Eventos católicos cercanos con filtros y volante | 47 | R9 | XL |
| M14 | 2-3 temas visuales seleccionables | 48 | R9 | L |
| M12 | Apartado social de donaciones | 49 | R9.1 | XL |

## 3. Decisiones del usuario (2026-08-07, con actualizaciones)

* **Operador legal del piloto de donaciones (2026-08-09)**: OraVia opera el piloto de Donaciones Solidarias bajo la figura de **persona individual** (el dueño del producto), difiriendo la constitución de una entidad a una fase posterior. Los documentos de `legal/` quedan con la figura fijada y los datos personales (nombre según DPI, domicilio contractual, contacto legal) por completar con el abogado, junto con la cláusula de jurisdicción propuesta. Riesgo aceptado y conocido: la responsabilidad recae en el patrimonio personal durante el piloto.

Tomadas por el dueño del producto durante la planificación:

* **Distribución en dos releases**: R8 "Orar con hondura" + R9 "Iglesia viva" + R9.1 "Manos abiertas", como se describe arriba.
* **Donaciones (M12)** — *actualizada el 2026-08-08 por la Especificación Funcional v2 del dueño del producto*: modelo de **reserva digital + Punto Comunitario de Encuentro + entrega directa** donante→receptor. La parroquia solo administra disponibilidad (ventanas, capacidad, fechas bloqueadas) y **nunca recibe, almacena, inspecciona ni custodia artículos**; quedan prohibidos los flujos Donante→Parroquia→Receptor y Donante→OraVia→Receptor. Se mantienen de la decisión original (2026-08-07): piloto acotado vía parroquias verificadas, sin direcciones personales, sin chat libre (solo mensajes estructurados). La decisión original de custodia parroquial con confirmaciones del admin queda **sustituida**. Detalle completo en spec 49 v2.
* **Encíclicas y Catecismo oficial (M2/M5)**: se incorporan como **enlaces oficiales** (vatican.va y fuentes oficiales) en Recursos Externos con tipo `web` y categorías. No se embebe texto → cero riesgo de licencia (CIC es de LEV; Youcat es editorial privada).
* **Moderación de eventos (M11)**: equipo editorial en el CMS + **vía rápida para administradores de parroquia verificados** (aprobación exprés/automática).
* **Temas visuales (M14)**: **variantes completas** (Camino, Juvenil, Sereno) en R9; en R8 se adelanta únicamente el selector claro/oscuro/sistema (hoy fijo en `ThemeMode.system`).
* **Retos (M9)**: **sí otorgan puntos** por día completado, validados en servidor con tope diario anti-abuso.

Decisiones recomendadas y documentadas (no bloquean el arranque, salvo la primera para su bloque):

* **D1 — Fórmula del Padre Nuestro por las intenciones del Papa**: ~~requiere dictamen previo~~ **Resuelta (2026-08-07)**: el dueño del producto aprobó avanzar con la fórmula tradicional; el dictamen formal del revisor teológico se documenta en el PR de implementación del bloque 38-B (gobernanza habitual de contenido doctrinal).
* **D4 — Enmienda a la regla de GPS**: se mantiene "la posición del usuario nunca viaja al servidor"; se añade la excepción explícita "la ubicación de un lugar (templo, evento) enviada por acción deliberada y consentida del usuario sí puede viajar". Registrar como nota de ADR.
* **D8 — Reflexión "¿qué me dijo Dios hoy?"**: se cifra localmente con `JournalCrypto` (AES-256-GCM), coherente con ADR-004; nunca viaja al servidor.
* **D9 — Anti-spam de push**: tope de frecuencia semanal por tipo, quiet hours, y el tipo "oración urgente" NO salta el opt-out del usuario.
* **Asesoría legal (spec 49)**: el disclaimer de donaciones se revisa con asesoría legal antes del lanzamiento del piloto. Borrador de trabajo entregado en `borrador-disclaimer-donaciones.md`; el dueño del producto lo valida con su abogado.

## 4. Mapa de dependencias

```
R8                                        R9
────────────────────────────────────      ─────────────────────────────────────
42-A (tipo web + categorías) ──► 42-B (encíclicas/catecismos oficiales)

45 (push server-side + CMS) ─────────────► 47-D (difusión push de eventos)

                                          46 (bucket parish-media + strip EXIF
                                              + cola CMS de aprobación)
                                               ├──► 47-C (volantes de eventos)
                                               ├──► 49-B (fotos de artículos)
                                               └──► 49-D (panel admin parroquial:
                                                    se extiende con ventanas de
                                                    encuentro, capacidad y bloqueos)
```

* Los recordatorios de planes (40-E), retos (41-C), eventos (47-D1) y entregas de donaciones (49-G2) usan el `ReminderScheduler` **local** existente (canales nuevos 1300+/1400+/1500+/1600+, siguiendo la convención 1001/1002/1100+/1200). **No dependen** del push remoto.
* **Coordinación 46↔49**: el spec 46 no cambia con la v2 de donaciones, pero quien implemente su panel de admin parroquial debe saber que 49 lo **extenderá con configuración de ventanas** (disponibilidad general), no con una cola de transacciones — la parroquia nunca ve operaciones individuales.
* M15 (spec 44) y la reflexión de planes (40-C) reutilizan `JournalCrypto` (`lib/core/crypto/journal_crypto.dart`), el mismo cifrado del Diario y de las sesiones de Reconciliación.
* El aviso de cuenta del Ranking (43-A) reutiliza `_LinkAccountPrompt`, extraído de `GroupsScreen` a widget compartido.
* Las imágenes del Catecismo (42-D) y las variantes de tema (48) pasan por `section_visuals.dart`, que sigue siendo la única fuente de verdad visual (regla de la casa + test de contraste AA).

## 5. Orden de implementación sugerido

### Release 8 — "Orar con hondura"

| Orden | Spec | Mejoras | Esfuerzo | Racional |
|---|---|---|---|---|
| 1 | 38 Rosario y Lectio | M1, M6 | S | Victoria rápida muy visible; D1 puede diferir el bloque B sin bloquear A/C. |
| 2 | 43-A Ranking | M7 | S | Corrige una confusión real de usuarios; reutiliza widget existente. |
| 3 | 39 Favoritos universales | M3 | M | Base transversal; conviene tenerla antes de tocar más pantallas. |
| 4 | 44 Reconciliación: notas | M15 | S | Reutiliza cifrado existente; alto valor pastoral. |
| 5 | 42 Recursos y Catecismo | M2, M5 | M | El bloque A (migración) desbloquea el B (encíclicas). |
| 6 | 41 Retos v2 | M9 | M | Corrige la pérdida de estado; añade persistencia y puntos. |
| 7 | 40 Planes de Lectura 2.0 | M4 | L | Bloques A/D/E primero; B (audio) y F (contenido) pueden solaparse. |
| 8 | 43-B Formación | M8 | S | Discovery + progreso por lección. |
| 9 | 45 Push comunitario | M10 | M | Cierra R8: su valor pleno se cobra en R9 (eventos). |

Además en R8: selector de tema claro/oscuro/sistema en "Yo" (adelanto acordado de M14; ver spec 48, bloque A0).

Si el calendario aprieta, los puestos 6–9 pueden cortarse como **R8.1** siguiendo el precedente R5.1.

### Release 9 — "Iglesia viva" (+ R9.1)

| Orden | Spec | Mejora | Esfuerzo | Racional |
|---|---|---|---|---|
| 1 | 46 Parroquias in situ | M13 | M | Funda bucket `parish-media`, strip EXIF y cola de aprobación en CMS. |
| 2 | 47 Eventos católicos | M11 | XL | Reutiliza bucket, moderación y push (spec 45). |
| 3 | 48 Temas visuales | M14 | L | Paralelizable: no depende de 46/47. |
| 4 | 49 Donaciones piloto (v2) | M12 | XL | R9.1, tras asesoría legal del disclaimer. La v2 (entrega directa + ventanas) amplía el alcance: si el calendario aprieta, partir en R9.1a (flujo núcleo: bloques A–E) y R9.1b (F–H, métricas), precedente R5.1. |

## 6. Riesgos

* **CMS de un solo HTML**: R9 añade tres colas de moderación nuevas (altas de parroquia, eventos, donaciones) sobre `apps/admin-cms/index.html`. La v2 de donaciones además suma al CMS: ciclo `CHANGES_REQUESTED`, tabla de políticas de categorías, cola de incidentes con `UNDER_REVIEW` y parámetros configurables (plazos, tolerancia, reglas de no-show). El mini-refactor del CMS al inicio de R9 pasa de recomendable a **necesario**.
* **Contenido doctrinal nuevo** (38-B fórmula del Papa, 42-C ampliación del Catecismo, 41-D retos nuevos, 40-F planes nuevos): pasa por revisor teológico según la gobernanza existente. El Catecismo interno sigue siendo redacción propia con referencias numéricas al CIC — nunca texto literal (derechos LEV).
* **Donaciones (49)**: sigue siendo el riesgo legal y de seguridad más alto del plan; por eso es piloto aislado en R9.1 con disclaimer revisado legalmente. La v2 **elimina el riesgo de custodia parroquial** (la parroquia nunca recibe ni almacena artículos; criterio de éxito: 0 artículos en custodia) pero **introduce el encuentro presencial directo entre desconocidos**, mitigado con: punto comunitario en ventanas establecidas, tokens de operación sin datos personales, confirmación digital doble, reglas de no-show, reporte de incidentes con `UNDER_REVIEW` y pauta de emergencias.
* **Privacidad**: toda nota espiritual nueva (faltas, reflexiones) es cifrada local y nunca sube al servidor; las fotos comunitarias se suben con strip de EXIF/geodatos; la posición del usuario nunca viaja (solo la de lugares, por acción explícita — D4).
* **Operación de push (45)**: definir quién puede enviar y con qué límites antes de habilitar el panel (D9); auditoría de envíos en tabla `push_campaigns`.

## 7. Verificación transversal

Regla de la casa: **cada AC se verifica con un test nombrado con su identificador** (p. ej. `test('AC-A1.1: …')`). Cada spec incluye su sección de verificación; además:

* Paridad i18n es/en para toda clave nueva (test de paridad existente).
* Contraste AA para todo uso visual nuevo (`section_visuals_contrast_test.dart`).
* Suites RLS en SQL para cada tabla nueva (patrón `scripts/*_rls_test.sql`), corriendo en CI.
* Los flujos cifrados verifican que nada sensible queda en claro en disco ni viaja por red.

## 8. Índice de specs

| Spec | Documento | Título |
|---|---|---|
| 38 | `spec-38-rosario-lectio.md` | Rosario y Lectio: pulido de oración |
| 39 | `spec-39-favoritos-universales.md` | Favoritos universales |
| 40 | `spec-40-planes-lectura-2.md` | Planes de Lectura 2.0 |
| 41 | `spec-41-retos-espirituales-v2.md` | Retos Espirituales v2 |
| 42 | `spec-42-recursos-catecismo.md` | Recursos y Catecismo ampliados |
| 43 | `spec-43-comunidad-formacion.md` | Comunidad y Formación: pulido |
| 44 | `spec-44-reconciliacion-notas.md` | Reconciliación: captura en el momento |
| 45 | `spec-45-push-comunitario.md` | Push comunitario |
| 46 | `spec-46-parroquias-in-situ.md` | Registro de parroquias in situ y fotos |
| 47 | `spec-47-eventos-catolicos.md` | Eventos católicos cercanos |
| 48 | `spec-48-temas-visuales.md` | Temas visuales |
| 49 | `spec-49-donaciones-piloto.md` | Donaciones Solidarias v2 (entrega directa en Punto Comunitario de Encuentro) |
