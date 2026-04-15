# Prompt Cookbook — Antes y Después

## Introducción

La mejor forma de interiorizar las técnicas de prompting es ver transformaciones reales: un prompt que no funciona y su versión corregida, con una explicación de **por qué** cada cambio mejora el resultado. Este cookbook presenta 5 casos comunes que encontrarás en tu trabajo diario.

---

## Caso 1: Prompt Vago vs. Específico

### Antes (ineficaz)

```text
Add authentication to the API
```

**Por qué falla**: no especifica qué tipo de autenticación, dónde implementarla, qué proteger, ni cómo verificar. El agente tomará decisiones arbitrarias sobre cada una de estas cosas.

### Después (efectivo)

```text
Add JWT authentication to the Express API in src/api/.
- Use the jsonwebtoken package (already in package.json)
- Add middleware in src/middleware/auth.js
- Protect all routes in src/api/routes/ except POST /login and POST /register
- Store JWT secret in environment variable JWT_SECRET
- Token expiration: 24 hours
- Write tests in test/auth.test.js covering:
  valid token, expired token, missing token, malformed token
- Run npm test to verify
```

**Qué cambió**: se especificó la tecnología (JWT), la ubicación (dónde crear archivos), las excepciones (rutas públicas), la configuración (variable de entorno, expiración) y la verificación (tests específicos + comando).

### Lección

> El costo de ser específico es 30 segundos extra escribiendo. El costo de ser vago es 10 minutos arreglando lo que el agente decidió por ti.

---

## Caso 2: Scope Creep vs. Tarea Acotada

### Antes (ineficaz)

```text
Fix the login bug, and while you're at it clean up the auth module,
add better error messages, and update the documentation
```

**Por qué falla**: son 4 tareas diferentes mezcladas en un solo prompt. El agente intentará hacer todas, pero al mezclar un fix con un refactor y documentación, es muy probable que introduzca regresiones o pierda el foco.

### Después (efectivo)

```text
The login endpoint returns 500 when email contains uppercase letters.
Reproduce: curl -X POST /login -d '{"email":"User@example.com","password":"test123"}'
Expected: 200 with token. Actual: 500 "Internal Server Error".
Fix only this bug. Don't refactor other code.
```

**Qué cambió**: una sola tarea, con reproducción exacta, resultado esperado vs. actual, y restricción explícita de scope.

### Lección

> Una tarea por prompt. Las otras 3 tareas se hacen después, cada una en su propio prompt con `/clear` entre ellas.

---

## Caso 3: Delegación Ciega vs. Delegación Informada

### Antes (ineficaz)

```text
Make the app faster
```

**Por qué falla**: "más rápido" no es un criterio medible. El agente no sabe qué parte de la app es lenta, cuál es el objetivo, ni dónde buscar.

### Después (efectivo)

```text
The /api/products endpoint takes 3.2s for 1000 products.
Target: under 500ms.
Likely bottleneck: N+1 queries in src/repositories/product.js
(each product fetches its category separately).
Investigate and propose a fix. Don't implement until I approve the approach.
```

**Qué cambió**: dato concreto (3.2s), objetivo medible (< 500ms), hipótesis de causa (N+1), ubicación del código, y control del desarrollador ("propón, no implementes").

### Lección

> Delegar no significa abdicar. Dale al agente la dirección, pero mantenete en el asiento del conductor.

---

## Caso 4: Error Sin Contexto vs. Error Con Reproducción

### Antes (ineficaz)

```text
TypeError: Cannot read properties of undefined (reading 'map')
Fix this error
```

**Por qué falla**: el agente no sabe dónde ocurre el error, qué lo provoca, ni en qué contexto. Puede haber docenas de `.map()` en el proyecto.

### Después (efectivo)

```text
Error in production:
TypeError: Cannot read properties of undefined (reading 'map')
  at ProductList (src/components/ProductList.tsx:23)
  at renderWithHooks (node_modules/react-dom/...)

Happens when: navigating to /products with no products in the database.
The API returns { products: null } instead of { products: [] } when empty.

Fix this defensively:
1. In src/components/ProductList.tsx: handle null/undefined products
2. In src/api/routes/products.ts: ensure the endpoint always returns an array
3. Add a test in test/api/products.test.js for the empty case
Run npm test to verify.
```

**Qué cambió**: stack trace completo, condición de reproducción, causa raíz identificada, fix en dos capas (frontend + backend), y test para prevenir regresión.

### Lección

> Un stack trace sin contexto de reproducción es como una dirección sin ciudad. Dale al agente el **cuándo** y el **cómo**, no solo el **qué**.

---

## Caso 5: Investigación Abierta vs. Investigación Dirigida

### Antes (ineficaz)

```text
Something is wrong with the database queries. Look into it.
```

**Por qué falla**: demasiado abierto. El agente puede pasar 10 minutos explorando cada query del proyecto sin saber qué buscar.

### Después (efectivo)

```text
Los usuarios reportan que la página de dashboard tarda >5s en cargar.
El endpoint GET /api/dashboard hace múltiples queries a PostgreSQL.

Investiga:
1. Cuántas queries ejecuta el endpoint (revisa src/api/routes/dashboard.ts)
2. Si hay queries N+1 (una query por cada ítem de una lista)
3. Si las queries usan índices (revisa prisma/schema.prisma)
4. Cuál es la query más lenta

No implementes nada. Reporta tus hallazgos con datos concretos:
- Número de queries
- Tiempo estimado de cada una
- Recomendación de qué optimizar primero
```

**Qué cambió**: síntoma específico, ubicación del código, preguntas concretas que responder, y petición explícita de reporte antes de actuar.

### Lección

> "Investiga" sin dirección genera exploración aleatoria. "Investiga X buscando Y en Z" genera resultados útiles.

---

## Patrón General de Transformación

Si tienes un prompt que no funciona, aplica estas 4 preguntas:

| Pregunta | Si la respuesta es "no"... |
|----------|---------------------------|
| **Hay datos concretos?** (números, errores exactos, archivos) | Añade el dato real, no la descripción vaga |
| **Hay scope claro?** (qué SÍ, qué NO) | Acota con restricciones explícitas |
| **Hay forma de verificar?** (test, comando, criterio medible) | Añade la verificación antes de pedir implementación |
| **Es una sola tarea?** | Separa en prompts independientes |

---

## Resumen

| Caso | Error principal | Corrección |
|------|----------------|------------|
| Vago vs. específico | Sin detalles técnicos | Tecnología + ubicación + config + verificación |
| Scope creep | Múltiples tareas mezcladas | Una tarea por prompt |
| Delegación ciega | Sin métrica ni dirección | Dato concreto + objetivo medible + hipótesis |
| Error sin contexto | Solo el mensaje de error | Stack trace + reproducción + fix en capas |
| Investigación abierta | "Mira a ver qué pasa" | Preguntas concretas + reporte antes de actuar |

---

## Siguiente

Continúa con [05 - Cuándo Dar Contexto vs. Dejar que el Agente Explore](05-contexto-vs-exploracion.md), donde aprenderás a decidir cuánto contexto dar en cada situación.
