# Procedimiento interno de Moderación, Incidentes y Seguridad (Documento F)

**DocumentID:** `OPS_MODERATION` · **Versión:** `v1.0` · **Carácter:** interno y operacional (sin efectos contractuales externos)
**Especificación origen:** Legal v2, §5, §16, §20, §22–§27, §37

---

## 1. Moderación de publicaciones

1.1. Toda publicación nace en estado `pending` y pasa moderación de contenido (texto y fotografías) contra la Política de Artículos (`ITEMS_POLICY`) antes de hacerse visible.

1.2. **Vocabulario obligatorio:** la moderación se denomina siempre "moderación de contenido" o "revisión de publicación". Prohibido en producto, soporte y marketing: "certificación", "inspección", "artículo verificado", "producto aprobado". Igualmente prohibido: "usuario seguro", "persona verificada", "donante confiable" — no existe proceso objetivo que sustente esas afirmaciones.

1.3. Resultados posibles: publicar, rechazar (con motivo registrado) o escalar a revisión de incidente.

## 2. Gratuidad — POLICY_VIOLATION

Todo intento de condicionar una entrega a dinero, propina obligatoria, trueque, servicios, compra de otro bien, comisión o compensación equivalente se registra como `POLICY_VIOLATION` y habilita, según proporcionalidad: cancelación de la operación; retiro de la publicación; advertencia; suspensión.

## 3. Ciclo de vida de incidentes

Estados obligatorios y ordenados:

`REPORTED → TRIAGED → UNDER_REVIEW → ACTIONED → CLOSED`

* **REPORTED:** entrada por reporte de usuario, notificación parroquial o detección interna. Se registra con timestamp y referencia a la operación/publicación.
* **TRIAGED:** clasificación por gravedad (seguridad personal > legal > política de artículos > conducta) y asignación.
* **UNDER_REVIEW:** investigación interna; preservación de evidencia asociada (registros, contenido, aceptaciones).
* **ACTIONED:** acción decidida y ejecutada. Acciones posibles: ninguna; advertencia; cancelación de operación; retirada de publicación; suspensión; preservación de evidencia; derivación a autoridades cuando legalmente corresponda.
* **CLOSED:** cierre con resumen y, si aplica, ajuste reputacional.

Cada transición queda en el audit trail con actor, timestamp y motivo.

## 4. Eventos con efecto reputacional

* **Rechazo en entrega:** el motivo registrado puede usarse para moderación, revisión, ajuste de reputación o suspensión. El rechazo nunca genera obligación para la parroquia.
* **No-show:** evento operacional, no deuda. Prohibida toda penalización económica. Reincidencia → restricciones proporcionales de uso (limitación temporal de publicaciones/solicitudes antes que suspensión).
* Los indicadores reputacionales publicados son solo verificables ("N entregas completadas", "0 no-shows en X meses"); prohibidas las etiquetas valorativas ("persona segura", "usuario confiable garantizado").

## 5. Requerimientos de autoridades

5.1. Todo requerimiento se canaliza al responsable designado, que verifica validez formal (autoridad competente, alcance, base legal) antes de responder; en caso de duda se consulta al abogado.

5.2. Se entrega únicamente la información estrictamente requerida y se registra el requerimiento, la verificación y la respuesta. Nunca prometer a usuarios que "los datos jamás se comparten".

## 6. Seguridad informática

Acceso administrativo bajo: roles definidos; principio de mínimo privilegio; registro de accesos; autenticación reforzada; protección de credenciales; backups; y procedimiento de respuesta a incidentes de seguridad. La seguridad tecnológica forma parte de la mitigación jurídica.

## 7. Audit trail de operaciones

Registrar de forma razonable y mínima: publicación; moderación; solicitud; selección; aceptación; ventana; cancelación; no-show; confirmación de entrega; confirmación de recepción; incidente. Retención según `RETENTION_POLICY`.
