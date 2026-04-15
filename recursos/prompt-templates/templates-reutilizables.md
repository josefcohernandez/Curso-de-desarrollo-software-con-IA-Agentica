# Prompt Templates Reutilizables

## Introducción

Esta colección de 8 templates cubre los tipos de tarea más frecuentes en desarrollo con IA agéntica. Cada template incluye la estructura, notas de uso y un ejemplo completo listo para adaptar.

Estos templates son la base — adáptalos a tu proyecto, stack y convenciones. Lo que importa no es la forma exacta sino que incluyan los 3 componentes esenciales: **contexto**, **intención** y **verificación**.

---

## Template 1: Fix Bug

### Estructura

```text
El test [nombre del test] falla con [error exacto].
El código relevante está en [archivos].
Investiga la causa raíz, implementa el fix y verifica que:
1. El test que fallaba ahora pasa
2. Los tests existentes no se rompen
3. No introduces regresiones en [área relacionada]
```

### Notas de uso

- Incluir siempre el **error exacto** (no "falla", sino el mensaje de error)
- Si tienes stack trace, inclúyelo — ahorra tiempo de investigación
- Pedir que no toque código fuera del área del bug evita cambios colaterales

### Ejemplo completo

```text
El test `test_create_task_with_empty_title` en tests/test_tasks.py:38 falla con:
  AssertionError: Expected status 400, got 201

El código relevante está en:
- src/api/routes/tasks.py (endpoint POST /tasks)
- src/api/validators.py (validación de input)

Investiga por qué el endpoint acepta un título vacío cuando debería rechazarlo.
Implementa el fix y verifica que:
1. El test que fallaba ahora pasa
2. Los otros 23 tests en tests/test_tasks.py siguen pasando
3. La validación funciona también para títulos con solo espacios en blanco
```

---

## Template 2: Add Feature

### Estructura

```text
Necesito [descripción concisa de la feature].
Contexto: [por qué se necesita, quién la usa].
Restricciones: [frameworks, patrones existentes a seguir].
El código existente relevante está en [archivos].
Escribe tests primero, luego implementa.
Verifica con [comando de test].
```

### Notas de uso

- Describir el "por qué" ayuda al agente a tomar mejores decisiones
- Siempre pedir TDD (tests primero) mejora la calidad del resultado
- Referenciar código existente para que siga el mismo estilo

### Ejemplo completo

```text
Necesito añadir paginación al endpoint GET /tasks.
Contexto: la lista de tareas puede tener miles de registros; el frontend necesita paginar.
Restricciones:
- Seguir el patrón de respuesta existente en src/api/routes/users.py (ya tiene paginación)
- Usar query params: ?page=1&limit=20
- Respuesta con formato: { data: [...], total: N, page: 1, limit: 20, pages: M }
- Límite máximo: 100 items por página
El código existente relevante está en src/api/routes/tasks.py y src/repositories/task.py.
Escribe tests primero en tests/test_tasks.py, luego implementa.
Verifica con: pytest tests/test_tasks.py -v
```

---

## Template 3: Explore Codebase

### Estructura

```text
Necesito entender cómo funciona [sistema/feature].
Dame un overview de:
1. Los archivos principales involucrados
2. El flujo de datos de principio a fin
3. Las dependencias externas
4. Cualquier quirk o decisión de diseño no obvia
No modifiques nada. Solo investiga y reporta.
```

### Notas de uso

- Usar esto al llegar a un proyecto nuevo o antes de un refactor grande
- La instrucción "no modifiques nada" es importante — evita cambios accidentales
- Puede complementarse con preguntas de seguimiento específicas

### Ejemplo completo

```text
Necesito entender cómo funciona el sistema de notificaciones.
Dame un overview de:
1. Los archivos principales involucrados
2. El flujo desde que se genera un evento hasta que el usuario recibe la notificación
3. Las dependencias externas (servicios de email, push, etc.)
4. Cualquier quirk o decisión de diseño no obvia (por qué se hizo así)
No modifiques nada. Solo investiga y reporta.
Incluye un diagrama de flujo en texto si ayuda a entender el proceso.
```

---

## Template 4: Refactor

### Estructura

```text
Quiero refactorizar [área] para [objetivo: legibilidad, performance, testabilidad].
Plan Mode: primero investiga el estado actual y propón un plan.
Restricciones:
- Mantener la API pública intacta
- Los [N] tests existentes deben seguir pasando
- No cambiar archivos fuera de [directorio]
```

### Notas de uso

- Siempre usar Plan Mode para refactors (primero planificar, luego ejecutar)
- Especificar el objetivo evita que el agente refactorice "de más"
- Los tests como restricción son la red de seguridad más importante

### Ejemplo completo

```text
Quiero refactorizar src/services/task_service.py para mejorar testabilidad.
Plan Mode: primero investiga el estado actual del archivo y propón un plan.
El archivo tiene 280 líneas con queries SQL directas mezcladas con lógica de negocio.
Objetivo: extraer las queries a src/repositories/task_repo.py siguiendo el repository pattern
que ya se usa en src/repositories/user_repo.py.
Restricciones:
- Mantener la API pública de TaskService intacta (mismos métodos, mismos parámetros)
- Los 18 tests en tests/test_task_service.py deben seguir pasando
- No cambiar archivos fuera de src/services/ y src/repositories/
Tras el refactor, ejecuta: pytest tests/ -v
```

---

## Template 5: Code Review

### Estructura

```text
Revisa los cambios en [archivos/branch/PR].
Busca específicamente:
- Bugs lógicos o edge cases no cubiertos
- Problemas de seguridad (OWASP top 10)
- Inconsistencias con el estilo del proyecto
- Oportunidades de simplificación
Reporta como lista priorizada: crítico > importante > sugerencia.
```

### Notas de uso

- Pedir priorización evita listas interminables de nits
- Incluir "OWASP top 10" como criterio asegura revisión de seguridad
- Complementar siempre con revisión humana — el agente no sustituye al reviewer

### Ejemplo completo

```text
Revisa los cambios en src/api/routes/auth.py y src/middleware/auth.py
(los archivos nuevos del feature de autenticación JWT).
Busca específicamente:
- Bugs lógicos o edge cases no cubiertos (token expirado, token malformado, etc.)
- Problemas de seguridad (OWASP top 10, especialmente A01-Broken Access Control y A02-Cryptographic Failures)
- Inconsistencias con el estilo del resto del proyecto en src/api/routes/
- Oportunidades de simplificación
Reporta como lista priorizada: crítico > importante > sugerencia.
No hagas cambios — solo reporta.
```

---

## Template 6: Migrate / Update

### Estructura

```text
Migrar [componente] de [versión/framework A] a [versión/framework B].
Documentación de migración: [enlace o archivo].
Empezar por [archivo más aislado] como prueba.
Tras cada archivo migrado, ejecutar [test] para verificar.
Si un paso no está claro, pregúntame antes de asumir.
```

### Notas de uso

- Empezar por el archivo más aislado permite validar el enfoque antes de escalar
- La instrucción "pregúntame antes de asumir" evita decisiones incorrectas en cadena
- Verificar después de cada archivo previene acumulación de errores

### Ejemplo completo

```text
Migrar el backend de Express.js v4 a Express v5.
Documentación de migración: https://expressjs.com/en/guide/migrating-5.html
Empezar por src/api/routes/health.js (el endpoint más simple) como prueba.
Tras migrar cada archivo, ejecutar: npm test para verificar que no hay regresiones.
Archivos a migrar en orden (de menos a más acoplados):
1. src/api/routes/health.js
2. src/api/routes/tasks.js
3. src/middleware/auth.js
4. src/middleware/error-handler.js
5. src/app.js (el entry point, al final)
Si un paso no está claro o la guía no cubre un caso específico, pregúntame antes de asumir.
```

---

## Template 7: Incident Response

### Estructura

```text
INCIDENTE: [descripción del problema en producción].
Impacto: [quién y qué está afectado].
Primeros datos: [logs, métricas, alertas disponibles].
Necesito:
1. Diagnóstico rápido de la causa probable
2. Propuesta de fix mínimo para restaurar servicio
3. Lista de verificaciones post-fix
NO hacer cambios todavía — primero diagnosticar.
```

### Notas de uso

- En incidentes, la velocidad importa — el prompt debe ser directo
- Pedir solo diagnóstico primero evita que el agente aplique fixes sin supervisión
- Incluir el impacto ayuda a priorizar correctamente

### Ejemplo completo

```text
INCIDENTE: el endpoint POST /tasks devuelve 500 desde hace 15 minutos.
Impacto: los usuarios no pueden crear tareas nuevas. GET /tasks funciona correctamente.
Primeros datos:
- Error en logs: "SequelizeConnectionError: SQLITE_BUSY: database is locked"
- El error empezó después del deploy de las 14:30
- El deploy incluyó cambios en src/services/task_service.py
Necesito:
1. Diagnóstico rápido: ¿qué cambió en el deploy que puede causar el lock?
2. Propuesta de fix mínimo para restaurar servicio (no refactor completo)
3. Lista de verificaciones post-fix para asegurar que no se repite
NO hacer cambios todavía — primero diagnosticar.
```

---

## Template 8: Post-Mortem

### Estructura

```text
Necesito documentar un post-mortem del incidente [descripción breve].
Datos del incidente:
- Inicio: [timestamp]
- Detección: [cómo se detectó]
- Resolución: [qué se hizo]
- Fin: [timestamp]
Genera un post-mortem con: timeline, causa raíz, impacto, acciones correctivas.
Formato: blameless (sin culpables, foco en el proceso).
```

### Notas de uso

- Los post-mortems deben ser "blameless" — foco en qué falló en el proceso, no en quién
- El agente es útil para estructurar la información, pero los datos deben ser tuyos
- Incluir siempre acciones correctivas con responsable y fecha

### Ejemplo completo

```text
Necesito documentar un post-mortem del incidente "database lock en POST /tasks".
Datos del incidente:
- Inicio: 2026-04-14 14:30 UTC (después del deploy)
- Detección: 14:45 UTC (alerta de error rate >5% en Datadog)
- Resolución: 15:10 UTC (rollback del deploy + fix de la transacción)
- Fin: 15:15 UTC (servicio restaurado, monitorización confirmada)
- Causa: el nuevo código abría una transacción de escritura y hacía un SELECT
  dentro de la misma transacción, causando un lock en SQLite
Genera un post-mortem con: timeline, causa raíz, impacto, acciones correctivas.
Formato: blameless (sin culpables, foco en el proceso).
Incluye al menos 3 acciones correctivas con tipo (prevención/detección/mitigación).
```

---

## Cómo Usar Estos Templates

1. **Copia y adapta**: no uses el template literalmente — reemplaza los placeholders con datos de tu proyecto
2. **Añade contexto específico**: cuanto más relevante sea el contexto, mejor será el resultado
3. **Combina templates**: para tareas complejas, puedes combinar elementos de varios templates
4. **Guarda los tuyos**: después de usar un template varias veces, crea tu versión personalizada

---

## Navegación

| | |
|---|---|
| **Recursos** | [Volver a recursos](../README.md) |
