# Política de Retención de Datos — Donaciones Solidarias

**DocumentID:** `RETENTION_POLICY` · **Versión:** `v1.0-borrador` · **Carácter:** interno
**Especificación origen:** Legal v2, §16–§17, §36

> **PENDIENTE:** los plazos marcados `[REVISIÓN LEGAL]` deben fijarse mediante revisión jurídica y técnica específica antes del piloto. Esta tabla debe existir en versión aprobada antes del Go/No-Go.

| Dato | Finalidad | Acceso | Plazo | Eliminación |
|---|---|---|---|---|
| Aceptaciones legales (UserID, DocumentID, Version, Language, Timestamp, AcceptanceAction, metadata de sesión) | Evidencia probatoria del consentimiento | Legal / administración restringida | Conforme a necesidad probatoria y obligaciones aplicables `[REVISIÓN LEGAL]` | Borrado o anonimización al vencer el plazo |
| Publicaciones activas (texto, fotos sin EXIF, condición) | Operar el apartado | Público (pseudonimizado) + moderación | Mientras la publicación esté activa | Retiro por el donante o moderación |
| Transacción / audit trail de operación (solicitud, selección, ventana, cancelación, no-show, confirmaciones) | Evidencia operacional, reputación, resolución de disputas | Moderación / administración restringida | Plazo definido `[REVISIÓN LEGAL — propuesta inicial: 24 meses]` | Anonimización de identificadores |
| Comunicación de coordinación (chat de operación, si existe) | Coordinar la entrega | Partes de la operación + moderación bajo incidente | Plazo limitado `[REVISIÓN LEGAL — propuesta inicial: 6 meses tras cierre]` | Borrado |
| Incidentes y reportes | Seguridad, moderación, cumplimiento | Equipo de incidentes / legal | Según naturaleza del incidente `[REVISIÓN LEGAL]` | Borrado o anonimización al cierre del plazo |
| Indicadores reputacionales (entregas completadas, no-shows) | Confianza basada en hechos verificables | Público por alias | Mientras la cuenta exista | Recalculo/anonimización al eliminar la cuenta |
| Datos analíticos | Mejora del servicio | Equipo de producto | Preferentemente agregados o anonimizados cuando ya no se requiera identificación | Agregación/anonimización continua |
| Registros de acceso administrativo | Seguridad informática | Seguridad / legal | `[REVISIÓN LEGAL — propuesta inicial: 12 meses]` | Borrado |

## Reglas transversales

1. Solo se conserva la información necesaria para la finalidad declarada (minimización).
2. El acceso sigue roles y mínimo privilegio; todo acceso administrativo queda registrado.
3. La eliminación de una cuenta dispara la revisión de todas las filas asociadas: se borra o anonimiza lo que no esté bajo retención obligatoria (aceptaciones, incidentes abiertos, requerimientos legales).
4. No se recopilan datos socioeconómicos de "necesidad" ni documentos de identificación durante el MVP; no existen, por tanto, filas de retención para ellos.
5. Esta política se versiona (`RETENTION_POLICY_v1.0`); los cambios de plazos se aprueban con criterio legal.
