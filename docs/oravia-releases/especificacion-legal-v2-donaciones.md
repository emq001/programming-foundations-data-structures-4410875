# OraVia — Donaciones Solidarias · Especificación Legal v2

**Arquitectura Legal by Design — Guatemala**
**Recibida del dueño del producto:** 2026-08-08 · **Sustituye:** enfoque de disclaimer único (spec 49, AC-A1.2, `borrador-disclaimer-donaciones.md`)
**Implementación:** carpeta `legal/` del repositorio (ver `legal/README.md` para el mapa documental)

## 1. Propósito

Esta especificación define la arquitectura jurídica y los controles de producto que deben gobernar Donaciones Solidarias.

Su principio rector es: **la protección jurídica no deberá descansar exclusivamente en cláusulas de exoneración.** El sistema deberá diseñarse operacionalmente para reducir los riesgos que posteriormente deban regularse contractualmente.

## 2. Arquitectura documental

Donaciones Solidarias no deberá depender de un único disclaimer. El marco documental deberá componerse de:

* **Documento A** — Términos Generales de Uso de OraVia.
* **Documento B** — Condiciones Específicas de Donaciones Solidarias.
* **Documento C** — Política de Privacidad de OraVia.
* **Documento D** — Política de Artículos Permitidos y Prohibidos.
* **Documento E** — Acuerdo de Participación de Parroquia.
* **Documento F** — Procedimiento interno de Moderación, Incidentes y Seguridad.

Los documentos A–E tendrán efectos externos según corresponda. El Documento F será principalmente operacional e interno.

## 3. Jerarquía contractual

Cuando exista contradicción respecto de Donaciones Solidarias:

1. legislación imperativa aplicable;
2. Condiciones Específicas de Donaciones Solidarias;
3. Términos Generales de OraVia;
4. políticas complementarias.

La Política de Privacidad gobernará específicamente el tratamiento de información personal.

## 4. Naturaleza jurídica del servicio

Las Condiciones deberán declarar que OraVia proporciona infraestructura tecnológica destinada a facilitar que usuarios puedan ofrecer y solicitar gratuitamente artículos. OraVia no adquiere la propiedad del artículo; no compra ni vende el artículo; no cobra comisión sobre la donación; no transporta el artículo; no almacena el artículo; no inspecciona físicamente el artículo; no actúa como representante del donante o receptor. La donación se perfecciona directamente entre las personas involucradas conforme a las circunstancias y legislación aplicable.

## 5. Gratuidad absoluta

Queda prohibido condicionar la entrega a: dinero; propina obligatoria; trueque; prestación de servicios; compra de otro bien; comisión; compensación económica o equivalente.

Un intento de cobro deberá constituir `POLICY_VIOLATION` y podrá causar: cancelación de operación; retiro de publicación; advertencia; suspensión.

## 6. Propiedad y facultad para donar

El donante deberá declarar expresamente: *"Declaro que soy propietario del artículo o tengo autoridad legal suficiente para disponer de él y donarlo."*

La declaración deberá quedar vinculada al `TransactionID` o `PublicationID`. OraVia no estará obligada mediante esta funcionalidad a verificar títulos de propiedad ordinarios, salvo cuando una categoría o circunstancia justifique controles adicionales.

## 7. Artículo y garantías

Las Condiciones deberán indicar claramente que OraVia: no prueba; no autentica; no certifica; no inspecciona físicamente; no determina calidad; no garantiza funcionamiento; no certifica procedencia; no garantiza seguridad; no garantiza aptitud para un uso determinado.

La moderación de contenido nunca deberá denominarse "certificación", "inspección", "artículo verificado" ni "producto aprobado" cuando esas actividades no hayan ocurrido.

## 8. Responsabilidad

No utilizar una exclusión absoluta del tipo "OraVia no será responsable bajo ninguna circunstancia". Utilizar: *"En la máxima medida permitida por la legislación aplicable…"* y conservar una cláusula de salvaguarda: *"Nada de estas condiciones pretende excluir o limitar derechos o responsabilidades que legalmente no puedan ser excluidos o limitados."*

## 9. Papel de la parroquia

La terminología contractual y de producto deberá utilizar **"Punto Comunitario de Encuentro"** y evitar "centro de recepción", "centro de almacenamiento", "depósito", "bodega" y "custodia", salvo que posteriormente se cree deliberadamente un modelo jurídico distinto.

La parroquia: proporciona un lugar; define ventanas disponibles; puede establecer reglas de acceso; puede cancelar o suspender una ventana; puede impedir una actividad por seguridad.

La parroquia no: recibe jurídicamente la donación; adquiere propiedad; almacena; custodia; transporta; inspecciona; certifica; garantiza; selecciona beneficiarios; representa a OraVia; representa a los usuarios.

## 10. Regla de no custodia

Las reglas de producto deberán prohibir expresamente dejar un artículo en la parroquia para recogida posterior.

Texto operacional recomendado: *"No dejes el artículo en la parroquia. Debes conservarlo contigo hasta entregarlo directamente al receptor."*

Si el receptor no se presenta: el donante conserva el artículo y se retira con él.

## 11. Convenio de parroquia participante

Cada parroquia deberá aceptar un Acuerdo de Participación independiente. Contenido mínimo:

a. **Objeto** — autorizar el uso de determinadas instalaciones como Punto Comunitario de Encuentro.
b. **Naturaleza** — no crea sociedad, agencia, mandato, relación laboral o representación.
c. **No custodia** — la parroquia no recibe artículos para almacenamiento.
d. **Horarios** — la parroquia controla sus ventanas y puede modificarlas.
e. **Seguridad** — puede impedir el acceso o finalizar una interacción.
f. **Marca** — definir uso permitido del nombre y logotipo parroquial.
g. **Datos** — limitar acceso a información exclusivamente necesaria.
h. **Incidentes** — definir canal de notificación a OraVia.
i. **Vigencia** — participación revocable conforme al acuerdo.
j. **Responsabilidad** — distribución razonable y jurídicamente válida de responsabilidades, sin pretender excluir obligaciones imperativas.

## 12. Mayoría de edad

El MVP deberá limitarse a personas de 18 años o más. Declaración: *"Declaro que tengo 18 años cumplidos o más."* No habilitar consentimiento parental durante el piloto.

## 13. Aceptación electrónica

La aceptación deberá ser afirmativa. No utilizar aceptación por silencio. No utilizar checkbox preseleccionado.

Registrar: `UserID`, `DocumentID`, `Version`, `Language`, `Timestamp`, `AcceptanceAction`, metadata de dispositivo/sesión cuando sea legalmente apropiado y necesario.

## 14. Versionado

Cada documento legal deberá disponer de versión inmutable (ejemplo: `DONATIONS_TERMS_v2.0`). Nunca modificar retroactivamente el contenido de una versión aceptada. Una actualización debe generar `DONATIONS_TERMS_v2.1` o `v3.0` según política de versionado.

## 15. Cambios materiales

Requerir nueva aceptación cuando exista modificación material, incluyendo por ejemplo: cambio en responsabilidades; nuevo rol de OraVia; incorporación de pagos; custodia; transporte; nuevos usos sustanciales de datos; cambios relevantes de elegibilidad; modificación relevante del papel parroquial.

Cambios meramente editoriales podrán documentarse sin necesariamente bloquear el servicio, sujeto a criterio legal.

## 16. Evidencia de una operación

Conservar audit trail razonable de: publicación; moderación; solicitud; selección; aceptación; ventana; cancelación; no-show; confirmación de entrega; confirmación de recepción; incidente.

Los registros deberán conservar únicamente la información necesaria durante períodos definidos en la política correspondiente.

## 17. Privacidad por diseño

Aplicar: minimización de datos; limitación de finalidad; acceso restringido; seguridad razonable; retención definida; eliminación cuando corresponda.

No recopilar datos socioeconómicos para establecer "necesidad" durante el MVP. No requerir documento de identificación salvo decisión posterior basada en riesgo y revisión jurídica. No compartir domicilios particulares.

## 18. Política de Privacidad

Deberá informar como mínimo: qué datos recopila OraVia; para qué; cuándo se muestran a otros usuarios; qué información recibe la parroquia; qué proveedores intervienen; retención; seguridad; mecanismos de solicitud del usuario; divulgaciones exigidas por ley; canales de contacto.

La política deberá revisarse periódicamente ante cambios legislativos.

## 19. Información visible

Principio: pseudonimización frente a otros usuarios siempre que sea compatible con seguridad y operación.

Mostrar: alias; historial operacional autorizado; datos del artículo; información necesaria sobre la operación.

No mostrar por defecto: DPI; nombre legal completo; domicilio; teléfono; correo; fecha de nacimiento completa; información financiera.

## 20. Moderación

La plataforma podrá: rechazar; retirar; suspender; limitar; investigar internamente; preservar evidencia; escalar incidentes.

Las Condiciones deberán reservar discreción razonable a OraVia, pero evitar una cláusula ilimitada o arbitraria incompatible con normas imperativas aplicables.

## 21. Artículos prohibidos

Las Condiciones incluirán categorías generales. Una Política de Artículos Permitidos y Prohibidos mantendrá el detalle operacional.

Categorías base prohibidas: armas y municiones; medicamentos; sustancias ilegales; alcohol; tabaco; bienes robados; falsificados; bienes cuya circulación sea ilegal; artículos peligrosos; dinero e instrumentos financieros; animales; perecederos; productos retirados del mercado; sustancias químicas peligrosas; productos vencidos que puedan representar riesgo.

OraVia podrá aplicar restricciones adicionales por seguridad.

## 22. Seguridad

Las Condiciones deberán aclarar que OraVia no verifica necesariamente antecedentes personales. El producto deberá evitar lenguaje como "usuario seguro", "persona verificada", "donante confiable", salvo que exista un proceso objetivo que permita sustentar jurídicamente dicha afirmación.

## 23. Sistema reputacional

Priorizar indicadores verificables. Permitidos: "12 entregas completadas.", "0 no-shows registrados durante los últimos X meses." Evitar: "Persona segura.", "Usuario confiable garantizado." La reputación no constituye aval de OraVia.

## 24. Rechazo en entrega

El receptor podrá rechazar el artículo. El rechazo no genera obligación para la parroquia. El artículo permanece con el donante. OraVia podrá utilizar el motivo para: moderación; revisión; ajuste de reputación; suspensión.

## 25. No-show

El no-show es un evento contractual/operacional de plataforma, no una deuda. No deberá generar penalización económica. Podrá generar restricciones proporcionales de uso ante reincidencia.

## 26. Incidentes

Crear proceso documentado: `REPORTED → TRIAGED → UNDER_REVIEW → ACTIONED → CLOSED`.

Posibles acciones: ninguna; advertencia; cancelación; retirada; suspensión; preservación de evidencia; derivación a autoridades cuando legalmente corresponda.

## 27. Denuncias y requerimientos legales

Debe existir proceso interno para responder a requerimientos válidos de autoridades. Las Condiciones y Política de Privacidad no deberán prometer que "nunca" se compartirán datos.

## 28. Protección al consumidor

Aunque la caracterización concreta de cada relación deberá validarse jurídicamente, las condiciones se redactarán conservadoramente para no depender de que un tribunal descarte totalmente la normativa de protección al consumidor.

Evitar: renuncias generales; exclusiones absolutas; facultades arbitrarias ilimitadas; cláusulas ilegibles; referencias ocultas a documentos no accesibles.

## 29. Legislación aplicable

Durante el piloto: leyes de la República de Guatemala.

Redacción: *"Estas condiciones se interpretarán de conformidad con las leyes de la República de Guatemala, sin perjuicio de los derechos y normas imperativas que resulten aplicables."*

La cláusula de jurisdicción y mecanismo de resolución de controversias deberá fijarse después de confirmar la entidad jurídica exacta que operará OraVia.

## 30. Idioma

Versión primaria del piloto: español. Cuando exista inglés: ambas versiones deberán estar controladas mediante versionado. Se deberá indicar qué versión prevalece en caso de discrepancia, conforme a la estrategia jurídica adoptada.

## 31. Identidad del operador

Antes del lanzamiento deberá quedar definido contractualmente: nombre legal completo del operador de OraVia; forma jurídica; domicilio contractual; correo o mecanismo legal de contacto; información adicional legalmente requerida.

No publicar condiciones definitivas sin identificar correctamente a la entidad operadora.

## 32. Diseño de interfaz legal

No mostrar todo el contrato en un modal pequeño ilegible.

Pantalla de aceptación — título: "Antes de usar Donaciones Solidarias". Resumen comprensible:

* Las donaciones son gratuitas.
* OraVia y la parroquia no inspeccionan los artículos.
* La entrega se realiza directamente entre usuarios.
* No dejes artículos almacenados en la parroquia.
* Debes tener 18 años o más.

Links visibles: "Leer Condiciones completas", "Política de Privacidad". Checkbox: "☐ Declaro que tengo 18 años o más." Botón: "Leí y acepto".

## 33. Aviso antes de publicar

*"Recuerda: el artículo debe ser tuyo o debes estar autorizado para donarlo. Debes describir su estado con honestidad y entregarlo gratuitamente."*

## 34. Aviso antes de solicitar

*"Podrás revisar el artículo antes de aceptarlo. OraVia y la parroquia no lo inspeccionan ni garantizan."*

## 35. Aviso antes de entrega

*"Entrega directamente al receptor. No dejes el artículo almacenado en la parroquia."*

## 36. Política de retención

Antes del piloto deberá existir tabla formal: dato / finalidad / acceso / plazo / eliminación.

Ejemplo conceptual — aceptación legal: conservar conforme a necesidad probatoria y obligaciones aplicables; transacción: plazo definido; chat: plazo limitado; incidente: plazo según naturaleza; datos analíticos: preferentemente agregados o anonimizados cuando ya no se requiera identificación.

Los plazos exactos deberán fijarse mediante revisión jurídica y técnica específica.

## 37. Seguridad informática

El acceso administrativo deberá utilizar: roles; principio de mínimo privilegio; registro de accesos; controles de autenticación; protección de credenciales; backups; procedimientos de incidentes.

La seguridad tecnológica forma parte de la mitigación jurídica.

## 38. Cambios que obligan a revisión legal previa

No implementar sin nueva revisión jurídica: custodia parroquial; delivery; transporte; almacenamiento; pagos; comisiones; trueques; menores; verificación formal de identidad; donaciones monetarias; evaluación socioeconómica; seguros; garantías; donaciones internacionales; entrega institucional a la parroquia.

## 39. Go/No-Go legal del piloto

No lanzar hasta completar:

* ☐ Entidad operadora definida.
* ☐ Términos Generales vigentes.
* ☐ Condiciones de Donaciones Solidarias aprobadas.
* ☐ Política de Privacidad alineada.
* ☐ Política de Artículos aprobada.
* ☐ Acuerdo con cada parroquia piloto.
* ☐ Registro versionado de aceptación.
* ☐ No custodia implementada funcionalmente.
* ☐ Flujo de incidentes funcionando.
* ☐ Estados y audit trail implementados.
* ☐ Reglas de edad implementadas.
* ☐ Textos UX consistentes con contratos.
* ☐ Validación final por abogado colegiado activo en Guatemala.

## 40. Principio jurídico rector

El texto contractual debe describir fielmente el funcionamiento real. No deberá intentarse solucionar mediante disclaimer una actividad que en la práctica convierta a OraVia o a la parroquia en custodio, transportista, vendedor, inspector o garante. La arquitectura técnica y la arquitectura jurídica deberán evolucionar juntas.
