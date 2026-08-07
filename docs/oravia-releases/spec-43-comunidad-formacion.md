# Spec 43 — Comunidad y Formación: pulido

**Release:** 8 "Orar con hondura" · **Mejoras origen:** M7, M8 · **Esfuerzo:** S + S · **Orden:** 2 (bloque A) y 8 (bloque B)
**Amplía:** specs 33 (Puntos y grupos) y 36 (Formación) · **ADRs:** 003 (identidad anónima progresiva)

## Contexto

**Ranking Global** (`global_ranking_screen.dart`): requiere cuenta vinculada (`hasAccount` en `groups_repository.dart:50-53`) pero, a diferencia de `GroupsScreen` (que muestra `_LinkAccountPrompt`), **no avisa nada al usuario anónimo** — este ve un switch que no persiste y el texto "Aún no hay participantes. ¡Sé el primero!", lo que explica la percepción de que "no funciona". La visualización es una lista plana sin podio; `RankingRow.isMe` existe pero no se rellena ni se usa.

**Formación** (`formation_courses.dart`): 3 cursos × 4 lecciones de texto plano, sin registro de qué lecciones se completaron.

## Bloque A — Ranking Global (M7)

### HU-A1 — Entender por qué no veo el ranking
Como usuario anónimo, quiero un mensaje claro que me diga que necesito vincular mi correo, con el botón para hacerlo.

* **AC-A1.1** — `_LinkAccountPrompt` se extrae de `GroupsScreen` a un widget compartido y se muestra en `global_ranking_screen.dart` cuando `!hasAccount`, con CTA a `/profile/account` (ADR-003). El switch de participación queda oculto o deshabilitado con explicación mientras no haya cuenta.

### HU-A2 — Podio y mi posición
Como participante, quiero un ranking más visual: podio con los 3 primeros y mi posición destacada.

* **AC-A2.1** — Cabecera tipo podio (1º elevado al centro, 2º y 3º a los lados) con alias, puntos y medallas visuales; el resto en lista numerada.
* **AC-A2.2** — La consulta del ranking (RPC `global_ranking`, SECURITY DEFINER) devuelve `is_me` — o el repositorio lo calcula contra el usuario autenticado — y `groups_repository.dart` rellena el campo `isMe` ya existente en `RankingRow`. La fila propia se resalta; si el usuario está fuera del top N, su fila aparece fija al pie con su posición real.
* **AC-A2.3** — Sin filtraciones: el podio solo muestra alias opt-in (jamás email/nombre real), igual que hoy.

## Bloque B — Formación (M8)

### HU-B1 — Nuevas opciones de formación (discovery)
Como dueño del producto, quiero una propuesta priorizada de nuevos contenidos de formación.

* **AC-B1.1** — Entregable de discovery (documento, no código) con catálogo propuesto y priorizado: Los sacramentos paso a paso · Historia de la Iglesia en 12 lecciones · Apologética básica (responder con caridad) · Oración para principiantes · Doctrina Social de la Iglesia · Vidas de santos por espiritualidad · Liturgia explicada (profundización de "La Misa") · Biblia: cómo leerla y no perderse. Cada curso nuevo sigue el formato bundled de `formation_courses.dart` con revisión editorial.

### HU-B2 — Guardar mi progreso
Como estudiante, quiero que la app recuerde qué lecciones ya completé.

* **AC-B2.1** — Progreso por lección en Drift (patrón idéntico al checkbox de planes de lectura) + indicador de avance por curso en `formation_courses_screen.dart` y check por lección en `formation_course_screen.dart`.

### HU-B3 — Autoevaluación (stretch)
Como estudiante, quiero un mini-quiz al final de cada lección para fijar lo aprendido.

* **AC-B3.1** — Quiz bundled de 3-5 preguntas por lección, sin servidor; puede caer a R9 si el calendario aprieta (marcado como stretch).

## Decisiones del usuario (2026-08-07)

* El bloque A se implementa temprano en R8 (orden 2) por ser la corrección más visible de una confusión real de usuarios.

## Verificación

* Un test por AC con su identificador; test de widget: pantalla de ranking con usuario anónimo muestra el prompt y no la lista engañosa.
* Test del RPC/repositorio: `isMe` correcto para el usuario autenticado; nunca se exponen emails.
* Persistencia del progreso de formación tras cerrar y reabrir la app.
