# OraVia — Marco documental legal

Implementa la **Especificación Legal v2** de Donaciones Solidarias (`docs/oravia-releases/especificacion-legal-v2-donaciones.md`). Principio rector: la protección jurídica no descansa exclusivamente en cláusulas de exoneración; el producto se diseña operacionalmente para reducir los riesgos (Legal by Design).

> **ESTADO: BORRADORES — PENDIENTES DE VALIDACIÓN.** Ningún documento de esta carpeta debe publicarse en la app hasta (a) completar los datos del operador — figura ya decidida: PERSONA INDIVIDUAL para el piloto (2026-08-09); faltan nombre según DPI, domicilio y contacto legal en los campos `[OPERADOR]` y (b) obtener la validación final de un abogado colegiado activo en Guatemala (spec v2, §31 y §39).

## Mapa documental (spec v2, §2)

| Doc | Archivo | DocumentID | Versión | Efectos |
|---|---|---|---|---|
| A | `terminos-generales.md` | `GENERAL_TERMS` | v1.0-borrador | Externos |
| B | `condiciones-donaciones-solidarias.md` | `DONATIONS_TERMS` | v2.0-borrador | Externos |
| C | `politica-de-privacidad.md` | `PRIVACY_POLICY` | v1.0-borrador | Externos |
| D | `politica-de-articulos.md` | `ITEMS_POLICY` | v1.0-borrador | Externos |
| E | `acuerdo-participacion-parroquia.md` | `PARISH_AGREEMENT` | v1.0-borrador | Externos (por parroquia) |
| F | `interno/procedimiento-moderacion-incidentes.md` | `OPS_MODERATION` | v1.0 | Interno |
| — | `interno/politica-de-retencion.md` | `RETENTION_POLICY` | v1.0-borrador | Interno |
| — | `interno/textos-ux-legales.md` | `UX_LEGAL_COPY` | v1.0 | Producto |
| — | `interno/checklist-go-no-go.md` | — | — | Interno |

## Jerarquía contractual (spec v2, §3)

En caso de contradicción respecto de Donaciones Solidarias prevalecen, en este orden:

1. la legislación imperativa aplicable;
2. las Condiciones Específicas de Donaciones Solidarias (Documento B);
3. los Términos Generales de OraVia (Documento A);
4. las políticas complementarias (Documentos C y D en sus ámbitos; la Política de Privacidad gobierna específicamente el tratamiento de información personal).

## Reglas de versionado (spec v2, §14–§15)

* Cada documento tiene un `DocumentID` y una versión inmutable (p. ej. `DONATIONS_TERMS_v2.0`). El contenido de una versión aceptada **nunca** se modifica retroactivamente: todo cambio genera una nueva versión (`v2.1` editorial, `v3.0` material).
* Un **cambio material** (responsabilidades, rol de OraVia, pagos, custodia, transporte, usos de datos, elegibilidad, papel parroquial) exige nueva aceptación afirmativa registrada. Los cambios editoriales se documentan sin bloquear el servicio, sujeto a criterio legal.
* La aceptación registra: `UserID`, `DocumentID`, `Version`, `Language`, `Timestamp`, `AcceptanceAction` y metadata de dispositivo/sesión cuando sea legalmente apropiado. Sin aceptación por silencio ni checkboxes preseleccionados (§13).
* Idioma primario del piloto: español. La versión en inglés, cuando exista, se controla por versionado y se indicará cuál prevalece (§30).

## Cambios que exigen revisión legal previa (spec v2, §38)

No implementar sin nueva revisión jurídica: custodia parroquial; delivery; transporte; almacenamiento; pagos; comisiones; trueques; menores; verificación formal de identidad; donaciones monetarias; evaluación socioeconómica; seguros; garantías; donaciones internacionales; entrega institucional a la parroquia.
