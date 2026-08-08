# Spec 49 — Donaciones Solidarias (piloto: entrega directa en Punto Comunitario de Encuentro)

**Release:** 9.1 "Manos abiertas" · **Mejora origen:** M12 · **Esfuerzo:** XL · **Versión:** 2 (2026-08-08, reemplaza a la v1 del 2026-08-07)
**ADRs:** 003 (cuenta vinculada) · **Dependencias:** spec 46 (bucket `parish-media` + strip EXIF, panel de admin parroquial), red de parroquias con admins verificados (specs 30/37/46), `ReminderScheduler` local (canales 1600+) · **Requisito previo:** asesoría legal del disclaimer (`borrador-disclaimer-donaciones.md`)

## Contexto

Un usuario ofrece gratuitamente un artículo que ya no necesita; otro lo solicita y lo recibe mediante un proceso seguro, trazable y respetuoso de la privacidad. **La v1 de este spec (2026-08-07) planteaba a la parroquia como intermediario con custodia** (el donante dejaba el artículo y el admin parroquial confirmaba recepción y entrega). **La v2 la reemplaza por decisión del dueño del producto (2026-08-08)**: la entrega es **directa entre donante y receptor**, y la parroquia participa únicamente como **Punto Comunitario de Encuentro** dentro de ventanas horarias que ella misma define — nunca recibe, almacena, transporta, inspecciona ni custodia artículos. OraVia aporta solo la coordinación digital.

## Principios funcionales obligatorios

El diseño debe cumplir permanentemente:

1. Toda entrega es gratuita.
2. OraVia nunca recibe ni transporta físicamente artículos.
3. La parroquia no almacena ni custodia artículos.
4. Donante y receptor realizan directamente la entrega.
5. No es necesario compartir domicilio, teléfono o correo personal entre usuarios.
6. Toda operación relevante queda registrada digitalmente.
7. La moderación de OraVia es de contenido y seguridad, no inspección física.
8. Un usuario puede retirarse de una operación antes de la entrega.
9. Ningún artículo se considera entregado hasta existir confirmación digital.
10. Las parroquias administran disponibilidad general, no transacciones individuales.

**Flujos prohibidos** (el sistema nunca los permite como flujo normal): `Donante → Parroquia → Receptor` y `Donante → OraVia → Receptor`. El único flujo válido es `Donante → Receptor`, con OraVia como coordinación digital y la parroquia como punto de encuentro.

**Prueba arquitectónica permanente**: toda funcionalidad futura de este apartado debe responder "no" a la pregunta *"¿hace que OraVia o la parroquia se acerquen a ser vendedor, transportista, almacenista, custodio, inspector o garante del artículo?"*. Un "sí" exige nueva evaluación funcional y legal antes de implementarse.

## Bloque A — Gobernanza, elegibilidad y aceptación legal

### HU-A1 — Marco de datos y auditoría antes de cualquier UI
Como equipo, queremos el modelo de datos, la máquina de estados y la trazabilidad resueltos primero.

* **AC-A1.1** — Tablas nuevas con RLS estricta (nadie ve del otro usuario más que el alias y sus contadores operativos): `donation_items` (publicaciones), `donation_requests` (solicitudes), `donation_transactions` (operación reservada con `DonationTransactionID`), `handoff_windows` (ventanas parroquiales), `donation_terms_acceptances` (aceptaciones legales), `donation_category_policies` (política de categorías), `donation_events` (auditoría), `donation_incidents` (reportes). Fotos en el bucket `parish-media` con strip de EXIF (spec 46).
* **AC-A1.2** — Ciclo de vida de una publicación: `DRAFT → PENDING_REVIEW → (CHANGES_REQUESTED ⇄ PENDING_REVIEW) → AVAILABLE → RESERVED → HANDOFF_SCHEDULED → COMPLETED`, con salidas `EXPIRED` (inactividad configurable), `CANCELLED`, `REMOVED` (moderación) y `SUSPENDED`. **Toda transición se valida en servidor**; las transiciones inválidas se rechazan.
* **AC-A1.3** — Bitácora de auditoría `donation_events`: cada evento crítico registra `EventID`, `TransactionID` (si aplica), `UserID` o `SystemActor`, `EventType`, `Timestamp`, `PreviousState`, `NewState`, `TermsVersion` cuando corresponda y metadata mínima. Eventos críticos: `PUBLICATION_CREATED`, `PUBLICATION_APPROVED`, `REQUEST_CREATED`, `RECIPIENT_SELECTED`, `RESERVATION_ACCEPTED`, `WINDOW_BOOKED`, `CANCELLED`, `NO_SHOW_REPORTED`, `HANDOFF_REJECTED`, `DELIVERY_CONFIRMED`, `RECEIPT_CONFIRMED`, `TRANSACTION_COMPLETED`, `INCIDENT_REPORTED`, `ACCOUNT_RESTRICTED`.

### HU-A2 — Elegibilidad y aceptación de condiciones
Como plataforma, solo permitimos operar a usuarios elegibles que aceptaron las condiciones vigentes.

* **AC-A2.1** — Para publicar o solicitar se requiere: cuenta activa **vinculada** (ADR-003), declaración expresa **"Declaro que tengo 18 años o más"**, aceptación de la versión vigente de las Condiciones de Donaciones Solidarias y no estar suspendido de la funcionalidad.
* **AC-A2.2** — Registro de aceptación: `UserID`, `TermsDocument`, `TermsVersion`, `Language`, `Timestamp`, `AcceptanceAction` (p. ej. `user_8271 · DONATIONS_TERMS · 2.0 · es-GT · 2026-08-08T15:32:13Z · ACCEPTED`). La aceptación es obligatoria antes de publicar y antes de solicitar.
* **AC-A2.3** — Re-aceptación: una versión nueva solo exige aceptar de nuevo cuando el cambio sea **material** según las reglas legales definidas; en ese caso bloquea publicar, solicitar o continuar una operación cuando jurídicamente corresponda.

### HU-A3 — Política configurable de categorías
Como equipo de moderación, queremos gobernar qué se puede donar sin codificar reglas jurídicas en la app.

* **AC-A3.1** — Tabla `donation_category_policies` administrada desde el CMS: cada categoría es `ALLOWED`, `RESTRICTED` o `PROHIBITED`, con parámetros opcionales: requiere moderación humana, tamaño máximo, condición mínima, fotografía obligatoria, declaración adicional y "prohibible por parroquia". Se aplica en la publicación (formulario) y en la moderación (cola).

## Bloque B — Publicación y moderación

### HU-B1 — Donar lo que ya no uso
Como donante, quiero publicar un artículo con información completa y honesta.

* **AC-B1.1** — Formulario obligatorio: título, categoría (de la tabla de políticas), descripción, **estado del artículo** con cinco valores estandarizados (`Nuevo / sin uso`, `Excelente estado`, `Buen estado`, `Usado / presenta desgaste`, `Requiere reparación menor`), **defectos conocidos**, fotografías reales (máx. 3, bucket `parish-media`, strip EXIF), parroquia o zona de entrega, confirmación de gratuidad y confirmación de propiedad/facultad para donar. Sin teléfono, dirección ni datos de contacto personales en ninguna parte del flujo.
* **AC-B1.2** — Cuatro declaraciones obligatorias (checkboxes registrados con la publicación): soy propietario o tengo autorización; la descripción y fotos representan razonablemente el estado actual; he informado los defectos o riesgos que conozco; lo entregaré gratis sin pedir dinero, servicios ni otra contraprestación.
* **AC-B1.3** — Las categorías pueden excluir estados incompatibles con seguridad (parámetro "condición mínima" de la política); el formulario lo aplica en cliente y el servidor lo revalida.
* **AC-B1.4** — Anti-abuso: tope de publicaciones activas por usuario. El donante puede editar mientras la publicación no esté `RESERVED`; una edición material (fotos, descripción, estado) regresa a `PENDING_REVIEW`. El donante puede retirar su publicación en cualquier momento previo a la entrega.

### HU-B2 — Moderación de contenido
Como equipo editorial, quiero revisar publicaciones antes de que se muestren.

* **AC-B2.1** — Toda publicación nace `DRAFT` y al enviarse pasa a `PENDING_REVIEW`. La moderación puede combinar reglas automáticas, detección de palabras, análisis de imágenes, revisión humana y reportes comunitarios. Resultados: `APPROVED` (→ `AVAILABLE`), `REJECTED` o `CHANGES_REQUESTED` (el donante corrige y reenvía). Cola nueva en el CMS.
* **AC-B2.2** — La aprobación significa únicamente que la publicación puede mostrarse en la plataforma; **no constituye certificación ni inspección del artículo**. Este texto aparece en el CMS y en las condiciones legales.

## Bloque C — Solicitud y selección del receptor

### HU-C1 — Solicitar un artículo
Como receptor, quiero pedir un artículo disponible sin exponer mis datos.

* **AC-C1.1** — Botón "Me interesa" sobre una publicación `AVAILABLE` crea una solicitud en estado `REQUESTED`. Solicitar no otorga derecho alguno sobre el artículo; una publicación puede acumular varias solicitudes; el receptor puede retirar la suya en cualquier momento.
* **AC-C1.2** — Antes de seleccionar, el donante ve **solo información operativa limitada** de cada solicitante: alias, donaciones recibidas completadas, entregas completadas y no-shows recientes si corresponde. Nunca información financiera, socioeconómica ni sensible.

### HU-C2 — Seleccionar receptor y reservar
Como donante, quiero elegir libremente a quién donar.

* **AC-C2.1** — El donante selecciona entre las solicitudes activas (MVP: selección libre). La solicitud elegida pasa a `SELECTED`, la publicación a `RESERVED` y las demás solicitudes a `WAITLISTED`.
* **AC-C2.2** — El receptor seleccionado tiene un plazo **configurable** (inicial: 24 h) para aceptar la reserva (`RESERVATION_ACCEPTED`). Si no acepta, la selección pasa a `SELECTION_EXPIRED` y el donante puede escoger otro solicitante de la lista de espera.

## Bloque D — Ventanas y administración parroquial

### HU-D1 — Configurar mi parroquia como Punto Comunitario de Encuentro
Como admin parroquial verificado, quiero ofrecer horarios de encuentro sin cargarme de gestión.

* **AC-D1.1** — Opt-in "Punto Comunitario de Encuentro" en `parish_admin_panel_screen.dart` (extiende el panel del spec 46, no crea uno nuevo). Configuración por parroquia: dirección pública del punto, instrucciones de acceso, **ventanas de encuentro** (día + rango horario), capacidad opcional `max_handoffs_per_window`, fechas bloqueadas, categorías localmente no aceptadas, suspensión temporal de una ventana y estado general `ACTIVE / PAUSED / INACTIVE`.
* **AC-D1.2** — La parroquia **no tiene cola de artículos ni vista de transacciones**: no decide quién recibe, no ve datos personales de las partes, no confirma calidad, no coordina usuarios. Como máximo ve ocupación agregada por ventana (n.º de operaciones programadas).
* **AC-D1.3** — Capacidad: al alcanzar `max_handoffs_per_window`, la ventana se marca `FULL` y no acepta nuevas operaciones. Las ventanas son comunitarias, no citas administradas individualmente por la parroquia.

### HU-D2 — Agendar la entrega
Como donante y receptor, queremos coincidir en una ventana disponible.

* **AC-D2.1** — Aceptada la reserva, ambos ven las ventanas habilitadas de la parroquia elegida (respetando fechas bloqueadas, suspensiones, capacidad y categorías locales). Donante y receptor seleccionan/confirman la **misma ventana** → la operación pasa a `HANDOFF_SCHEDULED` y se registra `WINDOW_BOOKED` contra la capacidad.

## Bloque E — Encuentro, credenciales y confirmación doble

### HU-E1 — Credenciales de la operación
Como plataforma, queremos completar la entrega sin exponer identidades.

* **AC-E1.1** — Cada operación tiene `DonationTransactionID` y dos credenciales independientes: `DonorToken` y `ReceiverToken`, representables como **QR y código alfanumérico alternativo** (formato tipo `DS-7K42-P9`) para cuando cámara o QR no estén disponibles. Los tokens son de un solo uso y no contienen datos personales.

### HU-E2 — Resultado de la entrega
Como donante y receptor, queremos cerrar la operación con confirmación digital de ambos.

* **AC-E2.1** — El artículo permanece bajo control del donante hasta el encuentro: la UI y las condiciones prohíben dejarlo en recepción, entregarlo a personal parroquial, almacenarlo en instalaciones o dejarlo para recogida posterior. El receptor puede inspeccionarlo visualmente antes de aceptar.
* **AC-E2.2** — **Confirmación doble**: receptor confirma "Recibí el artículo" y donante confirma "Entregué el artículo" (intercambio/escaneo de tokens). Solo con ambas confirmaciones válidas la operación pasa a `COMPLETED`, con fecha/hora registrada automáticamente. Nada se considera entregado sin confirmación digital.
* **AC-E2.3** — Rechazo presencial: el receptor puede rechazar con motivo estructurado (estado diferente al publicado, daño no informado, artículo incorrecto, preocupación de seguridad, otro) → `HANDOFF_REJECTED`. El artículo sigue con el donante, quien puede: devolver la publicación a `AVAILABLE`, editarla (pasa de nuevo por moderación) o cancelarla.

## Bloque F — Cancelaciones, no-show y reputación operativa

### HU-F1 — Cancelar sin fricción, con registro
Como usuario, quiero poder retirarme de una operación antes de la entrega.

* **AC-F1.1** — Antes de `HANDOFF_SCHEDULED`: cancelación permitida sin penalización (se registra; el abuso recurrente puede moderarse). Después de `HANDOFF_SCHEDULED`: permitida pero registrada como `LATE_CANCELLATION`. Una cancelación informada **nunca** cuenta como no-show.

### HU-F2 — No-show con reglas progresivas
Como plataforma, queremos desincentivar plantones sin expulsar por un único fallo.

* **AC-F2.1** — Período de tolerancia configurable (inicial: 30 min dentro de la ventana) antes de poder reportar `DONOR_NO_SHOW` (lo reporta el receptor) o `RECEIVER_NO_SHOW` (lo reporta el donante).
* **AC-F2.2** — Reglas progresivas **configurables en servidor** (valores iniciales): 1 no-show → registro operacional; 2 no-shows recientes → advertencia; 3 no-shows dentro del período configurable → restricción temporal. Un único no-show jamás implica expulsión automática.

### HU-F3 — Reputación operacional, no valoración personal
Como usuario, quiero ver hechos verificables de mi contraparte, no opiniones.

* **AC-F3.1** — **Sin calificación subjetiva de estrellas** en el piloto (reemplaza la valoración 1-5 de la v1). Por alias se muestran solo contadores verificables: donaciones completadas, recepciones completadas, cancelaciones tardías y no-shows confirmados. Servidos vía función SECURITY DEFINER (patrón `feature_ideas_ranked`) — nadie lee filas ajenas.

## Bloque G — Comunicación y recordatorios

### HU-G1 — Mensajes estructurados en lugar de chat libre
Como plataforma, queremos coordinar sin abrir un canal de chat en el MVP.

* **AC-G1.1** — Solo mensajes estructurados predefinidos entre las partes de una operación activa (p. ej. "Llegaré dentro de la ventana acordada", "Necesito cancelar", "Ya estoy en el punto de encuentro"). Sin chat libre en el piloto; los mensajes quedan registrados y son reportables. Si en el futuro se implementa chat libre, requerirá: retención conforme a política, reporte, filtrado de información sensible y bloqueo (fuera de alcance de este spec).

### HU-G2 — Recordatorios de entrega
Como usuario con una entrega agendada, quiero recordatorios oportunos.

* **AC-G2.1** — `ReminderScheduler` **local** (canales nuevos 1600+, siguiendo la convención 1001/1002/1100+/1200/1300+/1400+/1500+): recordatorio 24 h antes ("Tu entrega de Donaciones Solidarias está programada para mañana") y 2 h antes ("Recuerda llevar el artículo y tu código de entrega"). Los recordatorios nunca incluyen información personal de la contraparte.

## Bloque H — Incidentes, emergencias y privacidad

### HU-H1 — Reportar un problema
Como usuario, quiero reportar situaciones anómalas con categorías claras.

* **AC-H1.1** — Botón "Reportar un problema" con categorías: artículo prohibido, descripción engañosa, comportamiento inapropiado, intento de cobro, solicitud de información personal, fraude, seguridad, incidente en parroquia, otro. Los reportes van a la cola de moderación del CMS; un reporte grave coloca automáticamente la operación en `UNDER_REVIEW`. OraVia puede retirar contenido, suspender operaciones y limitar cuentas, con auditoría (`INCIDENT_REPORTED`, `ACCOUNT_RESTRICTED`). La parroquia también puede reportar incidentes relacionados con sus instalaciones.
* **AC-H1.2** — Emergencias: OraVia no se presenta como servicio de emergencia; la app indica que ante riesgo inmediato el usuario debe retirarse del lugar y acudir a las autoridades o servicios de emergencia correspondientes.
* **AC-H1.3** — Privacidad por defecto: nunca se muestran nombre legal completo, dirección, teléfono, correo ni documento de identidad; solo alias y contadores operativos. Toda comunicación necesaria ocurre por los mecanismos internos (HU-G1). Bloqueo de usuario disponible.

## Métricas y criterios de éxito del piloto

Derivadas de `donation_events` (sin instrumentación adicional): publicaciones creadas, tasa de aprobación, tiempo publicación → primera solicitud, solicitudes por artículo, tiempo reserva → entrega, tasa de entregas completadas, tasa de cancelaciones, tasa de no-show, tasa de rechazo presencial, incidentes por 100 transacciones, % de operaciones sin intervención humana, carga administrativa por parroquia, reutilización de donantes y de receptores.

Criterios de éxito para decidir la continuidad al cierre del piloto:

* ≥ 90 % de operaciones sin intervención manual parroquial.
* ≥ 85 % de reservas que terminan en entrega o cancelación previa.
* < 5 % de no-show.
* < 2 % de incidentes que requieren revisión humana.
* 0 artículos bajo custodia parroquial · 0 pagos gestionados por OraVia · 0 necesidad operacional de compartir domicilios particulares.

## Fuera del alcance del MVP

Transporte, delivery, bodega, custodia parroquial, pagos, trueques, menores de edad, evaluación socioeconómica, sistema de puntos, ranking de "necesidad", subastas, donaciones monetarias y donación institucional a la parroquia. Podrán ser productos independientes en fases futuras, sujetos a la prueba arquitectónica.

## Decisiones del usuario

* **2026-08-08 (v2, vigente)** — Modelo de reserva digital + Punto Comunitario de Encuentro + **entrega directa** donante→receptor. La parroquia solo administra disponibilidad (ventanas/capacidad/bloqueos), nunca transacciones ni artículos. Sin estrellas: reputación operacional por hechos verificables. Elegibilidad 18+ con declaración expresa. Mensajes estructurados, sin chat libre en el MVP.
* **2026-08-07 (v1, sustituida)** — ~~Entrega vía parroquia como punto neutral con confirmaciones del admin parroquial; valoración 1-5 tipo Uber.~~ Sustituida por la v2 en el modelo de entrega y la reputación; se mantienen de la v1: piloto acotado vía parroquias verificadas, sin direcciones personales, disclaimer con asesoría legal previa al lanzamiento.

## Verificación

* Un test por AC con su identificador (regla de la casa) y paridad i18n es/en de toda clave nueva.
* Suites RLS/SQL de todas las tablas nuevas (casos: anónimo, donante, receptor, solicitante en lista de espera, admin parroquial, moderador) — el admin parroquial no puede leer transacciones ni datos personales.
* Test exhaustivo de la máquina de estados en servidor: transiciones válidas del ciclo completo e inválidas rechazadas (incluye `SELECTION_EXPIRED`, `HANDOFF_REJECTED`, `LATE_CANCELLATION`, no-shows y `UNDER_REVIEW`).
* Test de tokens: un solo uso, sin datos personales, código alfanumérico equivalente al QR.
* Test de capacidad de ventana (`FULL` al llegar al tope) y de recordatorios locales (canales 1600+, sin datos de la contraparte).
* Piloto controlado: 2-3 parroquias, métricas y criterios de éxito definidos arriba, revisión al cierre para decidir continuidad.
