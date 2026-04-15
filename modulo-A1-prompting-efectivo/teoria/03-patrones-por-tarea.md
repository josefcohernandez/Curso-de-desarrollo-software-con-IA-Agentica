# Patrones de Prompt por Tipo de Tarea

## Introducción

El 90% de las tareas que le pides a un agente de código caen en una de 6 categorías. Cada categoría tiene un patrón óptimo — una estructura que produce los mejores resultados de forma consistente. Dominar estos 6 patrones es como tener 6 herramientas especializadas en tu caja de herramientas.

Cada patrón incluye: la estructura genérica (template), un ejemplo realista listo para copiar, y notas sobre cuándo usarlo.

---

## Patrón 1: Fix Bug

**Cuándo usarlo**: tienes un error conocido, un test que falla, o un comportamiento incorrecto reportado.

### Template

```text
El test [nombre del test] falla con [error exacto].
El código relevante está en [archivos].
Investiga la causa raíz, implementa el fix y verifica que:
1. El test que fallaba ahora pasa
2. Los tests existentes no se rompen
3. No introduces regresiones en [área relacionada]
```

### Ejemplo realista

```text
El test test/api/auth.test.js:67 "should reject expired tokens" falla con:
  AssertionError: expected 401 but got 200

El middleware de autenticación está en src/middleware/auth.js.
El helper que genera tokens de test está en test/helpers/token-factory.js.

Investiga por qué el token expirado no se rechaza.
Sospecho que el mock de Date.now() no se está aplicando correctamente.

Verifica que:
1. El test "should reject expired tokens" pasa
2. Los otros 12 tests de auth.test.js siguen pasando
3. Ejecuta npm test -- --grep "auth" para verificar todo el grupo
```

### Notas

- Siempre incluye el **mensaje de error exacto** — no lo parafrasees
- Si tienes una hipótesis de la causa, menciónala (ahorra tiempo de investigación)
- Pide explícitamente que no se rompan otros tests

---

## Patrón 2: Add Feature

**Cuándo usarlo**: necesitas agregar funcionalidad nueva a código existente.

### Template

```text
Necesito [descripción concisa de la feature].
Contexto: [por qué se necesita, quién la usa].
Restricciones: [frameworks, patrones existentes a seguir].
El código existente relevante está en [archivos].
Escribe tests primero, luego implementa.
Verifica con [comando de test].
```

### Ejemplo realista

```text
Necesito añadir un endpoint GET /api/users/:id/activity que devuelva
las últimas 50 acciones del usuario (login, edición, compra).

Contexto: el equipo de producto necesita un historial de actividad
para el panel de administración. Las acciones ya se registran en la
tabla activity_log (ver prisma/schema.prisma).

Restricciones:
- Sigue el patrón de los otros endpoints en src/api/routes/users.ts
- Usa el repositorio existente (no queries directas a la DB)
- Paginación con cursor (igual que GET /api/products)
- Solo accesible para roles admin y el propio usuario

Escribe los tests primero en test/api/users-activity.test.js.
Luego implementa. Ejecuta npm test para verificar todo.
```

### Notas

- "Escribe tests primero" es una instrucción poderosa — fuerza al agente a pensar en el comportamiento antes de implementar
- Referencia patrones existentes ("sigue el patrón de X") para mantener consistencia

---

## Patrón 3: Explore Codebase

**Cuándo usarlo**: necesitas entender cómo funciona algo antes de modificarlo, o estás en un codebase nuevo.

### Template

```text
Necesito entender cómo funciona [sistema/feature].
Dame un overview de:
1. Los archivos principales involucrados
2. El flujo de datos de principio a fin
3. Las dependencias externas
4. Cualquier quirk o decisión de diseño no obvia
No modifiques nada. Solo investiga y reporta.
```

### Ejemplo realista

```text
Necesito entender cómo funciona el sistema de pagos antes de modificarlo.

Investiga y repórtame:
1. Qué archivos están involucrados (desde el endpoint hasta Stripe)
2. El flujo completo de un pago: qué pasa desde que el usuario hace click
   en "Pagar" hasta que se confirma el cargo
3. Cómo se manejan los errores y reintentos
4. Qué webhooks de Stripe escuchamos y cómo se procesan
5. Si hay jobs asíncronos o colas involucradas

No modifiques ningún archivo. Solo investiga y dame un resumen estructurado.
```

### Notas

- La instrucción **"No modifiques nada"** es fundamental — sin ella, el agente puede decidir "mejorar" algo mientras explora
- Este patrón es ideal para onboarding en proyectos nuevos
- El resultado es un mapa mental que te ayuda a escribir mejores prompts para las tareas siguientes

> **Relación con el Curso de Claude Code**: el modo de exploración se potencia con el conocimiento de cómo el agente gestiona el contexto (ver [M03 - Contexto y Tokens](https://github.com/josefcohernandez/claude-code-course/blob/master/curso/modulo-03-contexto-y-tokens/README.md)).

---

## Patrón 4: Refactor

**Cuándo usarlo**: quieres mejorar la estructura del código sin cambiar su comportamiento.

### Template

```text
Quiero refactorizar [área] para [objetivo: legibilidad, performance, testabilidad].
Plan Mode: primero investiga el estado actual y propón un plan.
Restricciones:
- Mantener la API pública intacta
- Los [N] tests existentes deben seguir pasando
- No cambiar archivos fuera de [directorio]
```

### Ejemplo realista

```text
Quiero refactorizar src/services/order-service.ts. Actualmente tiene 450 líneas
con toda la lógica de órdenes en una sola clase. Objetivo: mejorar testabilidad
y legibilidad.

Primero investiga el archivo y propón un plan de refactoring. No implementes
hasta que yo apruebe el plan.

Restricciones:
- La interfaz pública de OrderService (los métodos que se llaman desde los controllers)
  debe mantenerse idéntica
- Los 28 tests en test/services/order-service.test.ts deben seguir pasando
- No toques archivos fuera de src/services/ sin consultarme
- Prefiero composición sobre herencia

Tras la implementación, ejecuta npm test -- test/services/ para verificar.
```

### Notas

- **Siempre usar Plan Mode** (o pedir plan primero) para refactors — implementar directamente es arriesgado
- Especifica el objetivo del refactor: "legibilidad" y "performance" producen resultados muy diferentes
- La restricción "tests deben seguir pasando" es tu red de seguridad

---

## Patrón 5: Code Review

**Cuándo usarlo**: quieres que el agente revise código existente, un PR, o cambios recientes.

### Template

```text
Revisa los cambios en [archivos/branch/PR].
Busca específicamente:
- Bugs lógicos o edge cases no cubiertos
- Problemas de seguridad (OWASP top 10)
- Inconsistencias con el estilo del proyecto
- Oportunidades de simplificación
Reporta como lista priorizada: crítico > importante > sugerencia.
```

### Ejemplo realista

```text
Revisa los cambios en el último commit (git diff HEAD~1).

Este commit añade validación de entrada al endpoint de registro de usuarios.
Busca específicamente:

1. ¿La validación cubre todos los campos requeridos?
2. ¿Hay inyección SQL o XSS posible a través de los inputs?
3. ¿Los mensajes de error revelan información sensible?
4. ¿El manejo de errores es consistente con el resto del API?
5. ¿Los tests cubren los edge cases (email duplicado, password débil,
   campos vacíos, payload malformado)?

Reporta como lista priorizada:
- CRÍTICO: bugs de seguridad o lógica que deben arreglarse antes del merge
- IMPORTANTE: problemas que deberían arreglarse pero no bloquean
- SUGERENCIA: mejoras opcionales de estilo o estructura
```

### Notas

- Pide **priorización explícita** — sin ella, el agente puede listar 20 "sugerencias" mezcladas con 1 bug crítico
- Especifica qué buscar para enfocar el review en lo que importa
- Este patrón se profundiza en el [Módulo A3 - Revisión de Código Generado por IA](../../modulo-A3-revision-codigo-ia/README.md)

---

## Patrón 6: Migrate/Update

**Cuándo usarlo**: necesitas migrar entre versiones de un framework, cambiar una dependencia, o actualizar una API.

### Template

```text
Migrar [componente] de [versión/framework A] a [versión/framework B].
Documentación de migración: [enlace o archivo].
Empezar por [archivo más aislado] como prueba.
Tras cada archivo migrado, ejecutar [test] para verificar.
Si un paso no está claro, pregúntame antes de asumir.
```

### Ejemplo realista

```text
Migrar el proyecto de Express 4 a Express 5.

Documentación de migración oficial:
https://expressjs.com/en/guide/migrating-5.html

Plan de ejecución:
1. Primero lee la guía de migración y lista los breaking changes
   que aplican a nuestro proyecto
2. Empieza por src/app.ts (el archivo principal) como prueba
3. Tras migrar app.ts, ejecuta npm test para verificar que no rompe nada
4. Luego migra los routers en src/api/routes/ uno por uno
5. Después los middlewares en src/middleware/

Tras cada paso, ejecuta npm test y reporta si algo falla.
Si un cambio no está claro o tiene múltiples opciones, pregúntame
antes de elegir.
```

### Notas

- **Migración incremental** es clave: un archivo a la vez, con verificación entre cada uno
- La instrucción "pregúntame antes de asumir" evita que el agente tome decisiones equivocadas en los casos ambiguos
- Proporcionar la documentación de migración (o su URL) ahorra mucho tiempo de exploración

---

## Tabla Resumen: Cuándo Usar Cada Patrón

| Patrón | Señales de que lo necesitas | Componente clave |
|--------|----------------------------|------------------|
| **Fix Bug** | Test que falla, error reportado, comportamiento incorrecto | Error exacto + archivo + verificación |
| **Add Feature** | Nueva funcionalidad, nuevo endpoint, nueva lógica | Contexto de negocio + restricciones + tests first |
| **Explore** | Codebase nuevo, sistema desconocido, pre-investigación | "No modifiques nada" + preguntas específicas |
| **Refactor** | Código difícil de mantener, deuda técnica, preparar para nueva feature | Plan Mode + tests como red de seguridad |
| **Code Review** | PR listo para merge, código nuevo que revisar, auditoría | Criterios específicos + priorización |
| **Migrate** | Cambio de versión, nueva dependencia, deprecaciones | Incremental + verificar entre pasos + documentación |

---

## Siguiente

Continúa con [04 - Prompt Cookbook: Antes y Después](04-cookbook-antes-despues.md), donde verás transformaciones reales de prompts ineficaces a prompts óptimos.
