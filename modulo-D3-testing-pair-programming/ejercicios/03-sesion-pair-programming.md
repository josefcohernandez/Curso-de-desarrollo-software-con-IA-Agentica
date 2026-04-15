# Ejercicio 3: Sesión de Pair Programming con los 5 Modos

## Objetivo

Realizar una sesión de 60 minutos de pair programming con un agente de IA, usando conscientemente los 5 modos de colaboración. El objetivo no es solo completar la tarea, sino **documentar las transiciones** entre modos y reflexionar sobre cuándo cada modo fue más efectivo.

---

## La Tarea: Sistema de Gestión de Notas

Vas a construir un módulo de notas con las siguientes funcionalidades:

1. Crear, leer, actualizar y eliminar notas (CRUD)
2. Buscar notas por contenido (texto completo)
3. Etiquetar notas con tags y filtrar por tag
4. Exportar notas a formato Markdown

Puedes usar el lenguaje y framework que prefieras (Python, TypeScript, etc.).

---

## Plan de Sesión: 60 Minutos

### Fase 1: Investigación Asistida (10 min)

**Modo: Investigación asistida**

Antes de escribir código, explora con el agente:

```text
Necesito diseñar un módulo de gestión de notas con CRUD, búsqueda por 
contenido, tags y exportación a Markdown. 

1. ¿Qué estructura de datos recomiendas para las notas?
2. ¿Qué librería de búsqueda full-text sería adecuada para un módulo pequeño?
3. ¿Cómo organizar los archivos del módulo?

Propón 2 opciones de arquitectura con pros y contras.
```

**Documenta**: Qué decidiste y por qué. ¿La exploración del agente te ahorró tiempo de investigación?

### Fase 2: Ping-Pong TDD para el CRUD (15 min)

**Modo: Ping-pong TDD**

Escribe tests uno a uno y deja que el agente implemente:

```text
Vamos a hacer Ping-pong TDD. Yo escribo el test, tú implementas.
Aquí va el primer test:

def test_create_note():
    store = NoteStore()
    note = store.create("Mi primera nota", content="Contenido de prueba")
    assert note.title == "Mi primera nota"
    assert note.content == "Contenido de prueba"
    assert note.id is not None
```

Escribe al menos 4 tests (create, read, update, delete) siguiendo el ciclo Red-Green-Refactor.

**Documenta**: ¿El agente intentó "anticipar" tests futuros? ¿Tuviste que frenar la sobre-ingeniería?

### Fase 3: Mob Programming para Tags (10 min)

**Modo: Mob programming**

Define la funcionalidad completa y deja que el agente la implemente mientras supervisas:

```text
Implementa el sistema de tags para las notas:
- Cada nota puede tener 0 o más tags (strings)
- Añadir y eliminar tags de una nota
- Filtrar notas por tag
- Listar todos los tags usados con su frecuencia
Incluye tests para cada funcionalidad. Sigue los patrones del código CRUD existente.
```

**Documenta**: ¿Necesitaste interrumpir al agente? ¿Cuántas veces? ¿Por qué?

### Fase 4: Driver/Navigator para la Búsqueda (15 min)

**Modo: Driver/Navigator**

La búsqueda full-text es más compleja. Tú escribes el código, el agente te guía:

```text
Voy a implementar la búsqueda full-text de notas. Voy a escribir el 
código paso a paso. Tu rol es:
- Sugerir el enfoque antes de que empiece
- Revisar cada paso que escribo
- Señalar edge cases que no estoy considerando
- NO escribir código por mí

Empiezo: ¿cuál es el mejor enfoque para búsqueda full-text en un 
conjunto pequeño de notas (< 10,000)?
```

**Documenta**: ¿Fue frustrante no dejar que el agente escribiera? ¿Te ayudó a entender mejor el código?

### Fase 5: Delegación para Exportación (10 min)

**Modo: Delegación completa**

La exportación a Markdown es una tarea bien definida y de bajo riesgo:

```text
Implementa la funcionalidad de exportación de notas a Markdown:
- export_note(note_id) → string en formato Markdown
- export_all(tag_filter=None) → string con todas las notas
- El formato debe incluir: título como H1, tags como badges, 
  fecha de creación, contenido
- Incluye tests completos
Avísame cuando termines para que revise.
```

**Documenta**: ¿El resultado fue aceptable a la primera? ¿Cuánto tardaste en revisarlo?

---

## Registro de Sesión

Usa esta plantilla para documentar la sesión en tiempo real:

```markdown
## Registro de Sesión de Pair Programming
Fecha: ____
Duración total: 60 min
Herramienta: ____

### Transiciones de modo

| Minuto | Modo anterior | Modo nuevo | Razón del cambio |
|--------|--------------|------------|------------------|
| 0 | - | Investigación | Inicio: necesito entender el problema |
| 10 | Investigación | Ping-pong TDD | Diseño claro, empiezo a implementar |
| 25 | Ping-pong TDD | Mob | CRUD funciona, tags son bien definidos |
| 35 | Mob | Driver/Navigator | Búsqueda es compleja, necesito control |
| 50 | Driver/Navigator | Delegación | Exportación es simple y acotada |

### Métricas de la sesión

- Prompts totales enviados: ___
- Tareas completadas: ___
- Ratio prompts/tarea: ___
- Veces que interrumpí al agente: ___
- Satisfacción general (1-5): ___

### Reflexión

1. ¿Qué modo fue más productivo para ti?
2. ¿Qué modo fue más frustrante?
3. ¿Cambiaste de modo en el momento correcto o demasiado tarde?
4. ¿Qué harías diferente en la próxima sesión?
```

---

## Entregable

1. El código funcional del módulo de notas (con tests)
2. El registro de sesión completado con transiciones y métricas
3. Reflexión de al menos 5 líneas sobre qué aprendiste de los modos de colaboración

---

## Criterios de Evaluación

- Se usaron los 5 modos durante la sesión (no vale usar solo Delegación)
- Las transiciones están documentadas con razón del cambio
- Las métricas de la sesión están completas
- La reflexión es específica (no genérica) y basada en la experiencia real
- El código resultante funciona y tiene tests
