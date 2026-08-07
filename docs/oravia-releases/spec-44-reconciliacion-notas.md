# Spec 44 — Reconciliación: captura en el momento

**Release:** 8 "Orar con hondura" · **Mejora origen:** M15 · **Esfuerzo:** S · **Orden:** 4
**Amplía:** spec 27 (Reconciliación) · **ADRs:** 004 (cifrado local) · **Dependencias:** ninguna

## Contexto

El Camino a la Reconciliación (flujo examen → resumen → guía → registro, `screens/reconciliation/`) ya guarda la sesión de examen como blob cifrado AES-256-GCM (`ReconciliationSessions.encryptedBody`, vía `JournalCrypto` con clave en Keychain/Keystore). Lo que **no existe** es una forma de anotar una falta en el momento en que se reconoce — días antes del examen — para no olvidarla. Este spec añade esa captura rápida con el mismo estándar de privacidad: cifrado local, nunca al servidor.

## Bloque A — Notas de examen cifradas

### HU-A1 — Anotar una falta al momento
Como penitente, quiero anotar una falta en el momento en que la reconozco, de forma privada y cifrada, para recordarla en mi próximo examen de conciencia.

* **AC-A1.1** — Entrada rápida desde la pantalla inicial de Reconciliación (y acceso directo desde "Orar") que guarda una nota corta **cifrada con `JournalCrypto` (AES-256-GCM)** en la tabla Drift nueva `reconciliation_notes` (blob cifrado + timestamp). Mismo tratamiento que `ReconciliationSessions.encryptedBody`: **nunca al servidor, nunca en claro en disco**.
* **AC-A1.2** — La lista de notas pendientes queda protegida por el mismo `BiometricGate` + `ScreenGuard` que la guía del sacramento; el contador visible fuera de la zona protegida no muestra contenido (solo "N notas guardadas" como máximo).
* **AC-A1.3** — Las notas se pueden editar y borrar individualmente (borrado real de la fila).

### HU-A2 — Recuperar mis notas en el examen
Como penitente, al iniciar un examen quiero ver mis notas pendientes e incorporarlas.

* **AC-A2.1** — El flujo del examen muestra las notas descifradas en memoria al inicio, con opción de incorporarlas al resumen (sección "De mis notas").
* **AC-A2.2** — Al registrar la confesión como realizada, las notas incorporadas **se purgan** (borrado real de filas), coherente con el borrado del examen por defecto. Test verifica la purga.
* **AC-A2.3** — Las notas no incorporadas sobreviven para el siguiente examen, con aviso al usuario.

## Decisiones del usuario (2026-08-07)

* Se mantiene la línea del spec 27: la app no clasifica ni juzga (sin campos de gravedad); las notas son texto libre del usuario.

## Verificación

* Un test por AC con su identificador. Tests críticos: (1) la nota no aparece en claro en la base Drift (inspección del blob), (2) cero tráfico de red en el flujo de notas, (3) purga efectiva tras registrar la confesión.
* QA manual del flujo completo con biometría activada y desactivada (fallback actual).
* Nota técnica: aprovechar para endurecer el fallback de clave sin almacenamiento seguro (`SecureJournalKey._randomKey()` deriva del timestamp — predecible; sustituir por CSPRNG), ya que este spec amplía el uso del cifrado.
