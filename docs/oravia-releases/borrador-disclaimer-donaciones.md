# Borrador — Disclaimer legal del apartado de Donaciones Solidarias

> **BORRADOR DE TRABAJO v2 — PENDIENTE DE VALIDACIÓN LEGAL.**
> Este texto NO debe publicarse en la app hasta ser revisado y aprobado por un abogado.
> Referencia: spec 49 v2 "Donaciones Solidarias (piloto: entrega directa en Punto Comunitario de Encuentro)", HU-A2.
> La v2 (2026-08-08) reemplaza al borrador del 2026-08-07, que describía a la parroquia como punto de recepción y entrega con custodia — modelo descartado por la Especificación Funcional v2.
> **SUSTITUIDO POR LA ESPECIFICACIÓN LEGAL v2 (2026-08-08).** El enfoque de disclaimer único fue reemplazado por una arquitectura documental completa: ver `especificacion-legal-v2-donaciones.md` (esta carpeta) y su implementación en `legal/` (mapa en `legal/README.md`). Las Condiciones vigentes en borrador son `DONATIONS_TERMS_v2.0` (`legal/condiciones-donaciones-solidarias.md`). Este archivo se conserva solo como antecedente histórico del texto que alimentó ese documento.

**Dónde se muestra:** pantalla de aceptación obligatoria antes de (a) publicar un artículo y (b) solicitar uno. La aceptación se registra con `TermsDocument`, `TermsVersion`, idioma, fecha/hora y acción (spec 49, AC-A2.2). El texto queda además disponible de forma permanente en la sección legal de la app (carpeta `legal/` del repositorio, junto a la política de privacidad).

---

## Texto propuesto para la app

### Condiciones del apartado de Donaciones Solidarias

**1. Qué es este espacio.**
El apartado de Donaciones Solidarias de OraVia es únicamente un espacio de coordinación digital que permite a los usuarios ofrecer gratuitamente artículos que ya no utilizan y a otros usuarios solicitarlos. **La entrega del artículo la realizan directamente el donante y el receptor**, en persona. OraVia **no es parte del intercambio**: no posee, no recibe, no inspecciona, no transporta, no almacena ni entrega los artículos, y no interviene en los acuerdos entre las personas.

**2. Requisito de edad.**
Este apartado es exclusivo para personas mayores de edad. Al usarlo declaras expresamente: **"Declaro que tengo 18 años o más."**

**3. Limitación de responsabilidad.**
OraVia **no se responsabiliza** por el desarrollo ni el resultado del intercambio, incluyendo —sin limitarse a— el estado real de los artículos, su idoneidad o seguridad, daños, pérdidas, accidentes o cualquier incidente ocurrido con ocasión del encuentro, de la entrega o del uso posterior de lo donado. OraVia **no verifica la identidad ni los antecedentes de las personas que donan o reciben donaciones, y no necesariamente tiene conocimiento de quiénes son**. La aprobación de una publicación significa únicamente que puede mostrarse en la plataforma; **no constituye inspección ni certificación del artículo**. Los historiales que se muestran son registros de actividad en la plataforma y no constituyen garantía alguna por parte de OraVia.

**4. Rol de la parroquia.**
Durante este programa, las parroquias participantes prestan voluntariamente un servicio de caridad: ofrecer sus instalaciones como **Punto Comunitario de Encuentro** dentro de horarios que ellas mismas establecen. La parroquia **no recibe, no almacena, no custodia, no inspecciona ni entrega artículos**, no decide quién recibe una donación y no coordina a los usuarios; por lo mismo, **no asume responsabilidad** por el estado, la idoneidad o el uso de los artículos, ni por los acuerdos o encuentros entre donantes y receptores. No está permitido dejar artículos en la recepción, entregarlos a personal parroquial ni dejarlos para recogida posterior.

**5. Responsabilidades del usuario.**
Al usar este apartado te comprometes a: (a) publicar información veraz y fotos reales del artículo, informando los defectos o riesgos que conozcas; (b) donar únicamente artículos de tu propiedad o sobre los que tengas autorización suficiente; (c) entregar los artículos gratuitamente, sin solicitar dinero, servicios ni ninguna otra contraprestación; (d) acudir al punto de encuentro en la ventana acordada o cancelar con aviso a través de la app; (e) cumplir la legislación aplicable en tu país; (f) tratar con respeto a las demás personas y a la comunidad parroquial.

**6. Artículos no permitidos.**
No pueden publicarse: armas de cualquier tipo; medicamentos y productos sanitarios; alcohol, tabaco u otras sustancias; alimentos perecederos; animales; artículos robados, falsificados, peligrosos o cuya circulación sea ilegal; dinero o instrumentos financieros. OraVia mantiene una política de categorías permitidas, restringidas y prohibidas, puede ampliar esta lista, y cada parroquia puede excluir categorías adicionales en sus instalaciones. OraVia puede retirar cualquier publicación que incumpla estas reglas.

**7. Privacidad.**
OraVia no comparte tus datos personales con la otra parte: no es necesario compartir domicilio, teléfono ni correo para completar una donación. Públicamente solo se muestran tu alias y tu historial operativo (donaciones y recepciones completadas, cancelaciones tardías y ausencias registradas). La comunicación entre las partes se realiza mediante los mecanismos internos de la app. El tratamiento de datos se rige por la Política de Privacidad de OraVia.

**8. Registro de la operación.**
Cada operación queda registrada digitalmente (solicitud, reserva, ventana acordada, confirmaciones, cancelaciones, ausencias e incidentes). Ningún artículo se considera entregado hasta que donante y receptor lo confirman en la app. Las cancelaciones tardías y las ausencias sin aviso quedan registradas y su reiteración puede limitar el uso del apartado.

**9. Moderación e incidentes.**
Toda publicación pasa por revisión de contenido antes de mostrarse. OraVia puede retirar publicaciones, suspender operaciones y suspender o limitar cuentas que incumplan estas condiciones o pongan en riesgo a la comunidad, con o sin aviso previo. Puedes reportar problemas desde la app. **OraVia no es un servicio de emergencia**: ante un riesgo inmediato, retírate del lugar y acude a las autoridades o servicios de emergencia correspondientes.

**10. Aceptación.**
Al pulsar "Acepto" declaras haber leído y comprendido estas condiciones. Tu aceptación queda registrada con fecha y hora. Si estas condiciones cambian de forma sustancial, se te pedirá aceptarlas de nuevo antes de seguir usando el apartado.

---

## Notas para la revisión del abogado (no se publican)

1. **Jurisdicción**: el mercado inicial es Guatemala; revisar la eficacia de la limitación de responsabilidad frente al Código Civil guatemalteco y normas de protección al consumidor (aunque el intercambio es gratuito, conviene confirmar que no aplican).
2. **Protección de datos**: alinear la sección 7 con la política de privacidad vigente (`legal/politica-de-privacidad.md`) y la normativa local aplicable.
3. **Menores de edad**: la Especificación Funcional v2 resuelve la pregunta abierta del borrador anterior — el apartado exige 18+ con declaración expresa (sección 2). Confirmar que la autodeclaración es suficiente en la jurisdicción o si se requiere algo adicional.
4. **Parroquias**: con el modelo de punto de encuentro (sin custodia) el texto de adhesión parroquial se vuelve más importante y a la vez más simple: la parroquia solo presta un espacio en horarios definidos. Redactar un documento breve de adhesión (rol voluntario de punto de encuentro, sin custodia ni responsabilidad, facultad de suspender su participación) — la sección 4 de este disclaimer debe ser consistente con él.
5. **Redacción de la sección 3**: incluye literalmente los dos puntos pedidos por el dueño del producto (no responsabilidad por el evento del intercambio; desconocimiento de las personas que reciben donaciones) — ajustar el alcance según el estándar local.
6. **Re-aceptación (sección 10)**: la v2 fija que solo un cambio **material** exige nueva aceptación (spec 49, AC-A2.3). Validar la formulación "de forma sustancial" y el criterio de materialidad.
7. **Encuentro presencial**: la v2 sustituye la entrega vía parroquia por la entrega directa entre usuarios en el punto de encuentro; valorar si conviene añadir recomendaciones de seguridad personal (acudir en horarios de la ventana, espacio comunitario) como texto informativo, sin que genere obligaciones de resultado para OraVia.
8. **Idioma**: la app es bilingüe; tras la validación en español se preparará la versión en inglés como traducción fiel.
