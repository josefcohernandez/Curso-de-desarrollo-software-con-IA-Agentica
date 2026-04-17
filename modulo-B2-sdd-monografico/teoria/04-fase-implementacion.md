# 04 — Fase 3: De la Spec al Código

## El principio de la sesión limpia

La implementación comienza en una sesión nueva — sin el contexto de la entrevista y la especificación.

¿Por qué? Porque el agente que escribió la spec tiene un sesgo: ha invertido esfuerzo mental en ese diseño y tiende a "ver" que la implementación lo cumple aunque no sea del todo así. Es el mismo sesgo cognitivo que hace que el autor de un código sea el peor revisor de ese mismo código.

Una sesión limpia llega a la implementación con ojos frescos. Su único contexto es SPEC.md y el código que genera.

El prompt de inicio de sesión de implementación:

```text
Implementa el sistema descrito en SPEC.md.
Lee la spec completa antes de escribir código.
Implementa en el orden de las fases definidas.
Al final de cada fase, ejecuta los tests y confirma que pasan.
Si encuentras algo en la spec que es ambiguo o incorrecto, para y dímelo
en lugar de tomar una decisión silenciosa.
```

---

## Implementación por fases

La spec define fases ordenadas por dependencias. La regla es simple: una fase debe estar completa y sus tests pasando antes de empezar la siguiente.

Por qué esto importa:

- Los errores de diseño se detectan en fase 1, cuando el coste de corrección es bajo
- Cada fase tiene un estado verificable ("funciona / no funciona") en lugar de un estado parcial difícil de evaluar
- Si hay que parar (deadline, cambio de prioridades), el sistema tiene una versión funcional en el estado de la última fase completada

### Ejemplo de implementación por fases de la API de tareas

**Fase 1: Estructura base**

```text
Implementa la Fase 1 de SPEC.md:
- Setup de base de datos y migraciones
- CRUD básico de tareas sin autenticación
- Tests de los endpoints básicos

No implementes la autenticación todavía (eso es Fase 2).
Al terminar, ejecuta los tests y muéstrame el output.
```

**Fase 2: Autenticación**

```text
La Fase 1 está completa y los tests pasan.
Ahora implementa la Fase 2: autenticación y autorización.
Lee la sección de requisitos no funcionales de SPEC.md para los detalles de JWT.
Al terminar, ejecuta todos los tests (Fase 1 + Fase 2) y confirma que ninguno rompe.
```

El patrón se repite para cada fase. La clave es siempre verificar que los tests de fases anteriores siguen pasando — las regresiones silenciosas entre fases son el error más común durante la implementación.

---

## Tests al final de cada fase

El momento correcto para escribir los tests es al final de cada fase, no al final de la implementación completa. Razones:

1. **Detectas errores de diseño temprano**: si una fase es difícil de testear, probablemente el diseño tiene un problema
2. **La red de seguridad crece con la implementación**: la Fase 2 se implementa con la protección de los tests de Fase 1
3. **Los tests de Fase 1 documentan el comportamiento base**: los tests de fases posteriores pueden referirse a ese comportamiento

Prompt para tests de una fase:

```text
La implementación de la Fase [N] está lista.
Escribe tests de integración para esta fase siguiendo el plan de testing de SPEC.md.
Cubre: todos los happy paths, los edge cases documentados en la spec, y los casos de error.
No mockees la base de datos — usa la base de datos real de test.
Ejecuta los tests y muéstrame el output.
```

---

## Combinando SDD con TDD

SDD y TDD son complementarios, no alternativos. La combinación es:

**Fase SDD**: genera la spec (el QUÉ).

**Fase BDD**: convierte los requisitos de la spec en features Gherkin (el CÓMO SE VERIFICA).

```text
Convierte los requisitos funcionales de SPEC.md a features Gherkin.
Un escenario por requisito, más escenarios para los edge cases documentados.
Guarda en features/tareas.feature
```

**Fase TDD**: implementa con el ciclo Red → Green → Refactor.

```text
Para cada escenario en features/tareas.feature:
1. Escribe el test que falla (Red)
2. Implementa el código mínimo para que pase (Green)
3. Refactoriza si es necesario (Refactor)
Empezar con el escenario: [nombre del primer escenario]
```

Esta combinación es la más robusta porque:
- La spec define el comportamiento esperado
- Gherkin lo hace verificable de forma legible
- TDD asegura que la implementación cumple exactamente lo especificado

---

## Ejemplo de feature Gherkin desde spec

A partir del requisito RF-05 de la spec de tareas ("El usuario solo puede ver sus propias tareas"):

```gherkin
Feature: Aislamiento de datos por usuario
  Como usuario autenticado
  Quiero ver solo mis propias tareas
  Para que mis datos privados estén protegidos

  Background:
    Given existen dos usuarios: "alice@example.com" y "bob@example.com"
    And "alice" tiene 3 tareas
    And "bob" tiene 2 tareas

  Scenario: El usuario ve solo sus tareas
    Given estoy autenticado como "alice"
    When hago GET /api/v1/tasks
    Then recibo 3 tareas
    And todas las tareas pertenecen a "alice"

  Scenario: El usuario no puede ver tareas de otro usuario
    Given estoy autenticado como "alice"
    When hago GET /api/v1/tasks/:id con un id de tarea de "bob"
    Then recibo 404
    And no se revela que la tarea existe

  Scenario: El usuario no puede modificar tareas de otro usuario
    Given estoy autenticado como "alice"
    When hago PATCH /api/v1/tasks/:id con un id de tarea de "bob"
    Then recibo 404
```

---

## Gestión de desviaciones durante la implementación

Es normal descubrir durante la implementación que algo en la spec era incorrecto, incompleto, o técnicamente inviable. El procedimiento correcto es:

1. **Para la implementación**. No improvises una solución alternativa silenciosamente.
2. **Documenta la desviación**: qué dice la spec, qué problema encontraste, qué alternativas existen.
3. **Decide**: ¿actualizas la spec y continúas, o escalas la decisión?
4. **Actualiza SPEC.md** con el cambio antes de continuar.

El prompt para gestionar una desviación:

```text
Al implementar [sección], encontré que [problema].
La spec dice [cita de la spec].
Las opciones son:
A) [opción A con implicaciones]
B) [opción B con implicaciones]
¿Cuál prefieres? Actualizaré la spec con tu decisión antes de continuar.
```

No hay desviaciones "pequeñas" que valga la pena ignorar. Una desviación no documentada es una spec que ya no refleja la realidad del sistema.

---

## Limpiar contexto entre fases

Para fases largas (más de 30 minutos de implementación), vale la pena limpiar el contexto entre fases y reinyectar solo lo necesario:

```text
[Después de reiniciar el contexto o abrir una sesión nueva]

Continuando la implementación de SPEC.md.
Fases completadas: 1 (CRUD base) y 2 (autenticación). Todos los tests pasan.
Implementa ahora la Fase 3: categorías.
El código actual está en src/. Lee los archivos relevantes antes de empezar.
```

Este patrón evita la degradación por contexto saturado que ocurre en sesiones largas.

---

## Checklist de implementación

Antes de dar una fase por completada:

```markdown
- [ ] El código de la fase está escrito y compila sin errores
- [ ] Los tests de la fase están escritos y pasan
- [ ] Los tests de fases anteriores siguen pasando (no hay regresiones)
- [ ] Los edge cases documentados en la spec están implementados
- [ ] No hay TODOs no planificados (o están documentados)
- [ ] El código sigue los patrones del proyecto (naming, estructura, estilo)
- [ ] Las desviaciones de la spec están documentadas en SPEC.md
```

---

> **En Claude Code**: usa una sesión nueva o el comando de limpieza de contexto entre fases largas. La spec está en el repositorio, así que el agente puede leerla con `Read` al inicio de cada sesión.
>
> **En Cursor**: abre una nueva pestaña de chat para cada fase. Referencia SPEC.md explícitamente en el primer mensaje.
>
> **En GitHub Copilot Chat**: inicia una nueva conversación para cada fase. Copilot tiene acceso al workspace, pero referencia SPEC.md explícitamente.
