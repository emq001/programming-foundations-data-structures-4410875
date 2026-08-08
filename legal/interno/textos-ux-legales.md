# Textos UX legales — Donaciones Solidarias

**DocumentID:** `UX_LEGAL_COPY` · **Versión:** `v1.0` · **Carácter:** copy de producto vinculante con los contratos
**Especificación origen:** Legal v2, §10, §32–§35

Regla: estos textos deben permanecer **consistentes con los documentos contractuales**. Cualquier cambio en Documento B que afecte estos mensajes obliga a actualizarlos en la misma release (checklist Go/No-Go: "Textos UX consistentes con contratos").

---

## 1. Pantalla de aceptación (antes de usar el apartado)

No mostrar el contrato completo en un modal pequeño ilegible: resumen + enlaces a los documentos completos.

**Título:** Antes de usar Donaciones Solidarias

**Resumen:**

* Las donaciones son gratuitas.
* OraVia y la parroquia no inspeccionan los artículos.
* La entrega se realiza directamente entre usuarios.
* No dejes artículos almacenados en la parroquia.
* Debes tener 18 años o más.

**Enlaces visibles:** "Leer Condiciones completas" · "Política de Privacidad"

**Checkbox (nunca preseleccionado):** ☐ Declaro que tengo 18 años o más.

**Botón:** Leí y acepto

Al pulsar el botón se registra la aceptación (`UserID`, `DocumentID`, `Version`, `Language`, `Timestamp`, `AcceptanceAction`).

## 2. Aviso antes de publicar

> Recuerda: el artículo debe ser tuyo o debes estar autorizado para donarlo. Debes describir su estado con honestidad y entregarlo gratuitamente.

Junto al aviso, el formulario de publicación incluye la declaración vinculada al `PublicationID`:

> ☐ Declaro que soy propietario del artículo o tengo autoridad legal suficiente para disponer de él y donarlo.

## 3. Aviso antes de solicitar

> Podrás revisar el artículo antes de aceptarlo. OraVia y la parroquia no lo inspeccionan ni garantizan.

## 4. Aviso antes de la entrega

> Entrega directamente al receptor. No dejes el artículo almacenado en la parroquia.

Texto operacional de no custodia (spec §10), visible también en las instrucciones de la ventana:

> No dejes el artículo en la parroquia. Debes conservarlo contigo hasta entregarlo directamente al receptor.

## 5. Vocabulario prohibido en la interfaz

* Sobre la parroquia: "centro de recepción", "centro de almacenamiento", "depósito", "bodega", "custodia" → usar **"Punto Comunitario de Encuentro"**.
* Sobre moderación: "certificación", "inspección", "artículo verificado", "producto aprobado" → usar "revisión de publicación" / "moderación de contenido".
* Sobre personas: "usuario seguro", "persona verificada", "donante confiable", "usuario confiable garantizado" → usar solo indicadores verificables ("12 entregas completadas", "0 no-shows registrados durante los últimos X meses").
