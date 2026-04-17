# Anatomía de un Buen Prompt

## Introducción

En el archivo anterior aprendiste los 3 componentes fundamentales de un prompt (contexto, intención, verificación). Ahora vamos a desglosar la estructura completa de un prompt efectivo en **5 componentes prácticos** que puedes usar como checklist mental cada vez que escribes un prompt.

---

## Los 5 Componentes de un Prompt de Desarrollo

| Componente | Qué incluir | Ejemplo |
|------------|-------------|---------|
| **Contexto del cambio** | Por qué estás haciendo este cambio. El problema de negocio o técnico que motiva la tarea | "Estamos migrando de REST a GraphQL porque los clientes móviles necesitan queries flexibles" |
| **Archivos relevantes** | Qué leer antes de actuar. Directorios, archivos, o patrones de nombre | "Revisa `src/api/routes/` y `src/models/`. Los tests están en `test/api/`" |
| **Restricciones** | Qué NO hacer. Qué respetar. Límites del scope | "No modifiques los tests existentes. No cambies la estructura de la base de datos" |
| **Resultado esperado** | Qué debería existir al terminar. Entregables concretos | "Un nuevo endpoint `/graphql` que soporte las queries `product` y `products`" |
| **Forma de verificar** | Cómo comprobar el resultado. Comandos, tests, criterios | "Ejecuta `npm test` y verifica que pasan los 47 tests existentes más los nuevos" |

No todos los prompts necesitan los 5 componentes explícitamente. Para tareas simples, 2-3 son suficientes. Pero cuando algo sale mal, casi siempre es porque falta uno de ellos.

---

## Ejemplo Completo: Los 5 Componentes en Acción

```text
# Contexto del cambio
Los usuarios reportan que el endpoint de búsqueda es lento cuando hay más de 10.000 productos.
El tiempo actual es ~4 segundos; el objetivo es menos de 500ms.

# Archivos relevantes
- src/repositories/product-repository.ts (la query principal)
- src/api/routes/products.ts (el endpoint)
- prisma/schema.prisma (el esquema de la DB)

# Restricciones
- No cambies la interfaz del API (mismos query params, misma estructura de respuesta)
- No añadas dependencias nuevas sin consultarme primero
- La solución debe funcionar con PostgreSQL (no usar features específicas de otro motor)

# Resultado esperado
- El endpoint GET /api/products?search=X responde en menos de 500ms con 10.000+ registros
- Si necesitas un índice en la DB, genera la migración con Prisma

# Verificación
- Ejecuta npm test para verificar que los 23 tests existentes siguen pasando
- Ejecuta: curl -w "%{time_total}" http://localhost:3000/api/products?search=laptop
  y verifica que el tiempo es < 0.5s
```

Este prompt tiene todo lo que un agente necesita para trabajar de forma autónoma y verificable.

---

## Anti-Patrones de Prompting

Estos son los errores más comunes que producen resultados pobres:

| Anti-patrón | Por qué falla | Alternativa |
|-------------|---------------|-------------|
| **"Arregla los tests"** | Demasiado vago: no especifica cuáles tests, qué error, ni qué se espera | "El test en `test/auth.test.js:45` falla con `TypeError: user.role is undefined`. Investiga la causa raíz y corrígelo" |
| **"Refactoriza este archivo"** | Sin criterio de éxito ni restricciones de scope | "Extrae las funciones de validación de `src/handlers.js` a un módulo `src/validators.js`. Los 12 tests existentes deben seguir pasando" |
| **"Haz lo que creas mejor"** | Delegación total sin dirección. El agente no conoce tu criterio | "Propón 2-3 enfoques para resolver X, con pros y contras de cada uno. Yo decido cuál implementar" |
| **Prompt de 500 palabras** | Saturación de contexto, posibles contradicciones internas | Acota a lo esencial. Si necesitas tanto contexto, usa una fase de planificación primero y luego implementa |
| **"Y también..." (scope creep)** | Acumular tareas no relacionadas en un solo prompt | Una tarea por prompt. Usa una sesión nueva entre tareas no relacionadas |
| **"Igual que hicimos ayer"** | El agente no tiene memoria entre sesiones salvo la que dejes en archivos persistentes del repositorio | Referencia archivos concretos: "Sigue el mismo patrón que `src/api/users.ts`" |
| **Copiar error sin contexto** | Pegar solo el mensaje de error sin qué se estaba haciendo | Incluye: qué comando ejecutaste, qué esperabas, qué obtuviste |

---

## Prompt Mínimo Viable vs. Prompt Completo

No todo necesita un prompt de 10 líneas. El nivel de detalle depende de la complejidad de la tarea:

### Prompt mínimo (tareas simples y claras)

```text
Añade un campo email_verified (boolean, default false) al modelo User en prisma/schema.prisma.
Genera la migración.
```

Esto basta porque la tarea es atómica, no ambigua, y el resultado es verificable con solo mirar el archivo.

### Prompt medio (tareas con algo de complejidad)

```text
El test test/payments.test.js:78 falla con "Stripe API key not set".
Revisa cómo se configura el mock de Stripe en los otros test files de payments
y aplica la misma configuración aquí.
Ejecuta npm test -- test/payments.test.js para verificar.
```

### Prompt completo (tareas complejas o con riesgo)

```text
Necesito migrar el sistema de autenticación de sessions (express-session + Redis)
a JWT (jsonwebtoken).

Contexto: tenemos 3 tipos de usuario (admin, editor, viewer) con permisos diferentes.
Los archivos relevantes están en src/auth/ y src/middleware/.

Plan:
1. Primero investiga el estado actual y proponme un plan de migración
2. Implementa paso a paso, ejecutando tests después de cada cambio
3. No elimines el código de sessions hasta que confirme que JWT funciona

Restricciones:
- Mantener backward compatibility durante la transición (ambos sistemas deben funcionar)
- Token expiration: 24h para admin, 8h para editor/viewer
- Refresh tokens con rotación

Verificar con: npm test (47 tests deben pasar)
```

---

## Checklist Rápido

Antes de enviar un prompt al agente, verifica mentalmente:

- [ ] **Queda claro qué hacer?** (no ambiguo)
- [ ] **Queda claro qué NO hacer?** (restricciones)
- [ ] **Hay forma de verificar?** (test, comando, criterio)
- [ ] **El scope es razonable?** (una tarea, no cinco)
- [ ] **Aporto valor con el contexto?** (no le cuento cosas que puede descubrir solo)

Si tres o más están sin marcar, el prompt necesita trabajo.

---

## Resumen

| Concepto | Descripción |
|----------|-------------|
| 5 componentes | Contexto, archivos, restricciones, resultado, verificación |
| Anti-patrones | Prompt vago, scope creep, delegación ciega, saturación, sin memoria |
| Nivel de detalle | Proporción al riesgo y complejidad de la tarea |
| Checklist | 5 preguntas antes de enviar cualquier prompt |

---

## Siguiente

Continúa con [03 - Patrones de Prompt por Tipo de Tarea](03-patrones-por-tarea.md), donde verás 6 templates reutilizables para las tareas más comunes en desarrollo.
