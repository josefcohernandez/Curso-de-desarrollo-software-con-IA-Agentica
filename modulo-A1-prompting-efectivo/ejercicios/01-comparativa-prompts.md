# Ejercicio 1: Comparativa de Prompts

## Objetivo

Escribir 3 versiones de un mismo prompt (vaga, razonable y óptima) para resolver un bug de autenticación. Ejecutar las 3 con tu agente de código y comparar los resultados en calidad, número de iteraciones y precisión.

**Tiempo estimado: 15 minutos**

---

## Contexto del Proyecto

Imagina que trabajas en un API REST de e-commerce con la siguiente estructura:

```text
ecommerce-api/
├── src/
│   ├── api/
│   │   └── routes/
│   │       ├── auth.ts          # Login, register, logout
│   │       ├── products.ts      # CRUD de productos
│   │       └── orders.ts        # Gestión de órdenes
│   ├── middleware/
│   │   └── auth-middleware.ts   # Verifica JWT en cada request
│   ├── services/
│   │   └── auth-service.ts     # Lógica de autenticación
│   └── models/
│       └── user.ts             # Modelo de usuario (Prisma)
├── test/
│   ├── auth.test.ts            # Tests de autenticación
│   └── orders.test.ts          # Tests de órdenes
├── package.json
└── prisma/schema.prisma
```

**El bug**: los usuarios con el rol `viewer` pueden acceder al endpoint `DELETE /api/products/:id`, que debería estar restringido solo a los roles `admin` y `editor`. El middleware de autenticación verifica el JWT correctamente, pero no comprueba los permisos por rol.

---

## Tarea

Escribe 3 versiones del prompt para resolver este bug:

### Versión 1: Prompt Vago

Escribe un prompt de 1-2 líneas, sin detalles técnicos ni archivos específicos. El tipo de prompt que se escribe cuando tienes prisa.

**Pista**: algo como "arregla los permisos" o "fix the auth bug".

### Versión 2: Prompt Razonable

Escribe un prompt de 3-5 líneas que mencione el problema, los archivos relevantes y pida verificación básica.

**Pista**: incluye el endpoint afectado, el rol que no debería tener acceso, y pide que ejecute tests.

### Versión 3: Prompt Óptimo

Escribe un prompt completo usando la estructura de 5 componentes (contexto, archivos, restricciones, resultado, verificación). 8-15 líneas.

**Pista**: incluye reproducción exacta, los archivos a revisar, qué roles tienen qué permisos, qué tests escribir, y cómo verificar.

---

## Ejecución

Si tienes acceso a un agente de código (Claude Code, Cursor, etc.):

1. Ejecuta cada prompt por separado, haciendo `/clear` entre cada uno
2. Para cada versión, anota:
   - Cuántas preguntas hizo el agente antes de actuar
   - Cuántos archivos modificó
   - Si el resultado fue correcto al primer intento
   - Cuántas iteraciones de corrección necesitaste

Si no tienes acceso, escribe las 3 versiones y analiza teóricamente qué haría el agente con cada una.

---

## Criterios de Evaluación

| Criterio | Versión vaga | Versión razonable | Versión óptima |
|----------|-------------|-------------------|----------------|
| **Claridad** | El agente tiene que adivinar qué arreglar | El agente sabe qué arreglar pero no los detalles | El agente tiene todo lo necesario |
| **Precisión del resultado** | Puede arreglar algo diferente al bug real | Probablemente arregla el bug correcto | Arregla exactamente el bug descrito |
| **Iteraciones necesarias** | 3-5 correcciones típicas | 1-2 correcciones | 0-1 correcciones |
| **Riesgo de regresión** | Alto (puede cambiar cosas no relacionadas) | Medio | Bajo (scope acotado) |
| **Tests incluidos** | Probablemente no añade tests | Puede añadir tests genéricos | Añade tests específicos para el caso |

---

## Reflexión

Después de completar el ejercicio, responde:

1. Cuánto tiempo extra te tomó escribir la versión óptima vs. la vaga?
2. Cuánto tiempo te ahorraste en correcciones con la versión óptima?
3. En tu trabajo diario, qué porcentaje de tus prompts se parecen a la versión vaga?

---

## Siguiente

Continúa con [Ejercicio 02: Prompt por Patrón](02-prompt-por-patron.md).
