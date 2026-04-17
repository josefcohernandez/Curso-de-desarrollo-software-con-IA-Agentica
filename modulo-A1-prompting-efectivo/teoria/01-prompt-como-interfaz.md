# El Prompt como Interfaz de Desarrollo

## Introducción

Si vienes de usar ChatGPT o Copilot Chat, probablemente piensas en el prompt como una **pregunta** que le haces a una IA. Con un agente de código, esa mentalidad te limitará. El prompt no es una pregunta — es una **especificación de trabajo** para un agente autónomo que va a leer tu código, tomar decisiones arquitectónicas, editar archivos y ejecutar comandos en tu terminal.

La diferencia es tan fundamental como la diferencia entre pedirle indicaciones a alguien en la calle y darle un brief a un desarrollador que acaba de unirse a tu equipo.

---

## Chatbot vs. Agente: Dos Paradigmas Diferentes

| Aspecto | Chatbot (ChatGPT, Copilot Chat) | Agente de código (Claude Code, Cursor, Cline) |
|---------|----------------------------------|------------------------------------------------|
| **Entrada** | Pregunta o petición | Especificación de trabajo |
| **Contexto** | Lo que pegas en el chat | Lee tu codebase completo |
| **Acciones** | Genera texto | Lee archivos, ejecuta comandos, edita código |
| **Decisiones** | Tú decides todo | El agente decide cómo implementar |
| **Verificación** | Tú copias y pruebas | El agente ejecuta tests y verifica |
| **Iteración** | Conversación lineal | Bucle autónomo (leer → planificar → implementar → verificar) |

El error más común de los nuevos usuarios de agentes de código es escribir prompts de chatbot:

```text
# Prompt estilo chatbot (ineficaz para un agente)
¿Cómo puedo añadir autenticación a mi API?
```

```text
# Prompt estilo agente (efectivo)
Añade autenticación JWT al API Express en src/api/.
Usa el paquete jsonwebtoken (ya está en package.json).
Protege todas las rutas excepto POST /login y POST /register.
Escribe tests en test/auth.test.js. Ejecuta npm test para verificar.
```

El primero pide información. El segundo da trabajo con criterios de éxito.

---

## Los Tres Componentes de un Prompt Efectivo

Todo prompt efectivo para un agente de código contiene tres componentes, explícitos o implícitos:

### 1. Contexto

Qué sabe (o debería saber) el agente sobre el proyecto, el problema y las restricciones.

```text
Estamos migrando de REST a GraphQL. El API actual está en src/api/
y los modelos en src/models/. Usamos TypeScript y Prisma como ORM.
```

El contexto le da al agente el **modelo mental** del proyecto. Sin él, el agente tiene que explorar desde cero — lo cual es válido en algunas situaciones, pero consume más tokens y tiempo.

> **Relación con el Curso de Claude Code**: el contexto permanente del proyecto suele vivir en un archivo de instrucciones persistentes del repositorio (`AGENTS.md`, `CLAUDE.md` o equivalente; ver [M04 - Memoria con CLAUDE.md](https://github.com/josefcohernandez/claude-code-course/blob/master/curso/modulo-04-memoria-claude-md/README.md)). El contexto del prompt complementa esa información con lo específico de la tarea actual.

### 2. Intención

Qué quieres que haga el agente, con qué restricciones y hacia qué resultado.

```text
Crea un nuevo resolver GraphQL para la entidad Product.
Debe soportar queries: product(id), products(filter).
Debe soportar mutations: createProduct, updateProduct.
No modifiques los resolvers existentes de User y Order.
```

La intención debe ser **específica y acotada**. Un prompt que dice "hazlo bien" no tiene intención útil. Un prompt que dice "crea un resolver con estas operaciones y estas restricciones" sí la tiene.

### 3. Verificación

Cómo va a comprobar el agente (y tú) que el trabajo está bien hecho.

```text
Ejecuta npm test para verificar que:
1. Los tests existentes siguen pasando (43 tests)
2. Los nuevos tests del resolver cubren al menos: query con id válido,
   query con id inexistente, mutation con datos válidos, mutation con datos inválidos
```

La verificación es el componente que más se omite — y el que más diferencia hace. Sin verificación, el agente no tiene forma de saber si terminó ni si lo hizo bien.

---

## La Regla de Oro

> **Si no le das forma de verificar, no le des forma de implementar.**

Esta regla cambiará tu forma de escribir prompts. Antes de pedir que el agente implemente algo, pregúntate: "¿Cómo va a saber que terminó? ¿Cómo va a saber que está bien?"

Si no puedes responder esas preguntas, necesitas más trabajo previo:

| Situación | Acción |
|-----------|--------|
| No hay tests para esta área | Pide primero que escriba los tests, luego que implemente |
| No sabes cuál es el comportamiento correcto | Pide primero que investigue y reporte, luego que implemente |
| El criterio de éxito es subjetivo ("que quede bonito") | Descompón en criterios objetivos o usa una fase de planificación para que proponga opciones |

---

## El Modelo Mental Correcto

Piensa en el prompt como un **brief para un desarrollador senior que acaba de unirse a tu equipo**. Este desarrollador:

- Es muy competente técnicamente
- No conoce tu proyecto, tus convenciones ni tu contexto de negocio
- Va a tomar las mejores decisiones posibles con la información que le des
- Si no le dices que algo es importante, puede ignorarlo
- Si le das instrucciones contradictorias, hará lo que le parezca más razonable

Eso significa que:

- **No hace falta explicar cómo programar** — sabe hacerlo
- **Sí hace falta explicar tu proyecto** — no lo conoce
- **Sí hace falta explicar tus restricciones** — no las asume
- **Sí hace falta explicar cómo verificar** — no sabe qué consideras "correcto"

---

## Resumen

| Concepto | Descripción |
|----------|-------------|
| El prompt no es una pregunta | Es una especificación de trabajo para un agente autónomo |
| Chatbot vs. agente | El chatbot responde, el agente actúa |
| Los 3 componentes | Contexto + Intención + Verificación |
| La regla de oro | Sin verificación, no hay implementación confiable |
| Modelo mental | Brief para un desarrollador senior nuevo en el equipo |

---

## Siguiente

Continúa con [02 - Anatomía de un Buen Prompt](02-anatomia-buen-prompt.md), donde desglosamos la estructura completa de un prompt efectivo con ejemplos detallados.
