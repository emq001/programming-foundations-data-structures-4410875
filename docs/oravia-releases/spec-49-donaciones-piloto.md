# Spec 49 — Donaciones solidarias (piloto parroquial)

**Release:** 9.1 "Manos abiertas" · **Mejora origen:** M12 · **Esfuerzo:** XL
**ADRs:** 003 (cuenta vinculada) · **Dependencias:** spec 46 (bucket `parish-media` + strip EXIF), red de parroquias con admins verificados (specs 30/37/46) · **Requisito previo:** asesoría legal del disclaimer

## Contexto

Funcionalidad nueva de punta a punta: un apartado social donde los usuarios donan cosas que ya no usan. Es la pieza de mayor riesgo legal y de seguridad de todo el plan (encuentros entre desconocidos, datos personales, responsabilidad). **Decisión del usuario:** piloto acotado en R9.1 donde la entrega se coordina **exclusivamente a través de parroquias verificadas** como punto neutral — sin direcciones personales, sin chat directo. Se evalúa el piloto antes de considerar el intercambio directo.

## Bloque A — Gobernanza y modelo

### HU-A1 — Marco seguro antes de cualquier UI
Como equipo, queremos el modelo de datos, la moderación y la cobertura legal resueltos primero.

* **AC-A1.1** — Tabla `donation_items`: título, descripción, `condition` CHECK (`'nuevo'`,`'buen_estado'`,`'usado'`), fotos (bucket `parish-media`, strip EXIF), instrucciones, `parish_id` del punto de entrega (obligatorio en el piloto), ciclo `pending → published → reserved → delivered → closed`; toda publicación nace `pending` y pasa moderación editorial (patrón existente). RLS estricta: nadie ve datos del donante más allá del alias.
* **AC-A1.2** — **Disclaimer de no responsabilidad** (texto nuevo en `legal/`, revisado con asesoría legal): OraVia no se responsabiliza por el intercambio ni necesariamente conoce a las personas que reciben donaciones. Aceptación obligatoria registrada con timestamp **al publicar y al solicitar**.

## Bloque B — Publicar un artículo

### HU-B1 — Donar lo que ya no uso
Como donante, quiero publicar un artículo con fotos, su estado e instrucciones para la entrega.

* **AC-B1.1** — Formulario con fotos (máx. 3), condición e instrucciones; requiere **cuenta vinculada** (ADR-003); sin teléfono, dirección ni datos de contacto personales visibles en ninguna parte del flujo.
* **AC-B1.2** — Tope de publicaciones activas por usuario (anti-abuso); el donante puede retirar su publicación en cualquier momento.

## Bloque C — Entrega vía parroquia (núcleo del piloto)

### HU-C1 — Coordinar a través de mi parroquia
Como donante o receptor, quiero que la entrega se coordine a través de una parroquia verificada como punto neutral.

* **AC-C1.1** — El receptor solicita el artículo; el donante lo entrega en la parroquia elegida; el **admin de parroquia confirma recepción y entrega** en la app (dos confirmaciones que mueven el ciclo `reserved → delivered`). Ni donante ni receptor ven datos personales del otro; la parroquia es el único punto de contacto.
* **AC-C1.2** — Las parroquias participan opt-in: el admin habilita "punto de donaciones" en su panel (`parish_admin_panel_screen.dart`).

## Bloque D — Valoración e historial

### HU-D1 — Confianza tipo Uber
Como usuario, quiero valorar la experiencia y ver el historial de quien dona o recibe.

* **AC-D1.1** — Valoración 1-5 disponible **solo tras `delivered` confirmado**, una por parte; historial/reputación pública (donaciones completadas, promedio) por alias, servida vía función SECURITY DEFINER (patrón `feature_ideas_ranked`) — nadie lee filas ajenas.

## Bloque E — Seguridad

### HU-E1 — Protecciones para ambas partes
Como usuario, quiero mecanismos de protección claros.

* **AC-E1.1** — Reportar publicación/usuario (cola de moderación), bloqueo de usuario, strip de EXIF en todas las fotos, cero geolocalización personal en el flujo, y retiro/baja de publicación por moderación con auditoría.

## Decisiones del usuario (2026-08-07)

* Piloto **exclusivamente vía parroquias verificadas**; se evalúa antes de abrir modalidad directa.
* El disclaimer pasa por asesoría legal antes del lanzamiento.

## Verificación

* Un test por AC con su identificador; suite RLS/SQL completa de `donation_items` y las valoraciones (casos: anónimo, donante, receptor, admin parroquial, moderador).
* Test del ciclo de estados (transiciones inválidas rechazadas en servidor).
* Piloto controlado: 2-3 parroquias, métricas de éxito definidas antes (artículos entregados, incidencias, carga del admin parroquial) y revisión al cierre para decidir la continuidad.
