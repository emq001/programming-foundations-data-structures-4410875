# Go/No-Go legal del piloto — Donaciones Solidarias

**Carácter:** interno · **Especificación origen:** Legal v2, §39
**Regla:** no lanzar el piloto hasta completar TODOS los ítems.

| # | Ítem | Estado | Evidencia / referencia |
|---|---|---|---|
| 1 | Entidad operadora definida (nombre legal, forma jurídica, domicilio, contacto legal) | ◐ Figura decidida: persona individual para el piloto (2026-08-09); faltan los datos personales | Completar `[OPERADOR]` en Documentos A, B, C y E con nombre según DPI, domicilio y contacto legal |
| 2 | Términos Generales vigentes | ☐ Pendiente (borrador `GENERAL_TERMS_v1.0`) | `legal/terminos-generales.md` |
| 3 | Condiciones de Donaciones Solidarias aprobadas | ☐ Pendiente (borrador `DONATIONS_TERMS_v2.0`) | `legal/condiciones-donaciones-solidarias.md` |
| 4 | Política de Privacidad alineada | ☐ Pendiente (borrador `PRIVACY_POLICY_v1.0`) | `legal/politica-de-privacidad.md` |
| 5 | Política de Artículos aprobada | ☐ Pendiente (borrador `ITEMS_POLICY_v1.0`) | `legal/politica-de-articulos.md` |
| 6 | Acuerdo firmado con cada parroquia piloto | ☐ Pendiente | `legal/acuerdo-participacion-parroquia.md` + Anexo I por parroquia |
| 7 | Registro versionado de aceptación implementado (afirmativo, sin preselección, con `UserID/DocumentID/Version/Language/Timestamp/AcceptanceAction`) | ☐ Pendiente | Spec 49 AC-A1.2 + spec v2 §13 |
| 8 | No custodia implementada funcionalmente (sin flujo de "dejar artículo"; instrucciones y estados lo impiden) | ☐ Pendiente | Spec v2 §10 |
| 9 | Flujo de incidentes funcionando (`REPORTED→TRIAGED→UNDER_REVIEW→ACTIONED→CLOSED`) | ☐ Pendiente | `legal/interno/procedimiento-moderacion-incidentes.md` |
| 10 | Estados de operación y audit trail implementados | ☐ Pendiente | Spec 49 AC-A1.1 + spec v2 §16 |
| 11 | Reglas de edad implementadas (18+, declaración, sin consentimiento parental) | ☐ Pendiente | Spec v2 §12 |
| 12 | Textos UX consistentes con contratos | ☐ Pendiente | `legal/interno/textos-ux-legales.md` |
| 13 | Validación final por abogado colegiado activo en Guatemala | ☐ Pendiente | Todos los documentos A–E + retención |

## Recordatorios

* La cláusula de jurisdicción/resolución de controversias (Documentos A, B y E) se fija **después** del ítem 1.
* La tabla de retención (`RETENTION_POLICY_v1.0`) necesita plazos aprobados antes del ítem 4.
* Cambios listados en spec v2 §38 (custodia, pagos, transporte, menores, etc.) reabren este checklist y exigen nueva revisión legal.
