# Escenario: Onboarding a un Codebase Desconocido

## El problema

Es tu primer día en un proyecto con 200K+ líneas de código. No conoces la arquitectura, las convenciones ni las herramientas. Tu equipo espera que seas productivo "pronto", pero "pronto" puede significar semanas o meses sin la estrategia correcta.

Con un agente de código, puedes comprimir ese proceso a **90 minutos** y salir con un mapa mental del proyecto y tu primer PR enviado.

---

## Workflow en 4 pasos

### Paso 1: Exploración inicial (15 minutos)

El objetivo es obtener un mapa de alto nivel del proyecto: tecnologías, estructura y puntos de entrada.

```text
Give me a high-level overview of this codebase:
- Main technologies and frameworks
- Directory structure and what each top-level folder contains
- Entry points (main files, server startup, CLI entry)
- Key data models and their relationships
Don't modify anything. Only investigate and report.
```

**Qué esperar**: el agente lee el `package.json` (o equivalente), explora la estructura de directorios, identifica archivos de configuración y te presenta un resumen estructurado.

**Consejo**: no te pierdas en los detalles. En esta fase solo necesitas responder: "¿qué hace este proyecto y cómo está organizado?"

### Paso 2: Deep dive en áreas clave (30 minutos)

Una vez que tienes el mapa general, profundiza en el flujo más importante del sistema. Elige el flujo que más te convenga según tu rol:

```text
Trace the authentication flow from login to session creation.
Show me the relevant files and the sequence of function calls.
Include: middleware involved, database queries, token generation,
and any external services called.
```

**Variantes según el dominio**:

- API REST: trazar una request desde el router hasta la respuesta
- Frontend SPA: seguir el flujo de datos desde la acción del usuario hasta la actualización de la UI
- Microservicios: mapear la comunicación entre servicios para un caso de uso concreto
- Data pipeline: seguir un dato desde la ingestión hasta el output final

**Consejo**: pide al agente que sea explícito con los archivos y números de línea. Esto te permite navegar el código después sin depender del agente.

### Paso 3: Entender las convenciones (15 minutos)

Cada proyecto tiene reglas no escritas. Descubrirlas por tu cuenta lleva semanas; el agente las encuentra en minutos.

```text
What patterns and conventions does this project follow?
- Naming conventions (files, variables, functions)
- Testing patterns (framework, structure, where tests live)
- Error handling patterns
- API design patterns (REST conventions, response format)
- Code organization (layers, modules, dependency injection)
```

**Qué buscar en la respuesta**:

| Aspecto | Ejemplo de lo que deberías anotar |
|---------|-----------------------------------|
| Naming | "Archivos en kebab-case, funciones en camelCase" |
| Tests | "Jest con archivos `*.test.ts` al lado del código fuente" |
| Errores | "Clase `AppError` centralizada en `src/errors/`" |
| API | "Respuestas envueltas en `{ data, error, meta }`" |

**Consejo**: guarda estas convenciones en un archivo de notas personal o en tu CLAUDE.md del proyecto. Las necesitarás para escribir código consistente.

### Paso 4: Primera contribución (30 minutos)

Nada consolida el onboarding como hacer un cambio real. Pide al agente que te encuentre algo accesible:

```text
Find a small, well-contained bug or TODO that I can fix as my first PR.
It should:
- Touch 1-2 files maximum
- Have existing tests nearby that I can run to verify
- Not require deep domain knowledge
- Be something that provides real value (not just cosmetic)
```

**Alternativas si no hay bugs ni TODOs claros**:

- Mejorar un test existente que solo cubre el happy path
- Añadir tipado a una función sin types
- Documentar una función compleja que descubriste en el paso 2
- Corregir un warning del linter que nadie ha atendido

**Consejo**: el objetivo no es impresionar con un cambio grande, sino demostrar que entiendes el workflow del proyecto (branch, test, PR, review).

---

## Resultado esperado

Al terminar los 4 pasos (~90 minutos), deberías tener:

1. **Un mapa de arquitectura** del proyecto: tecnologías, estructura, flujos principales y convenciones
2. **Un primer PR** enviado y listo para review, por pequeño que sea
3. **Confianza** para navegar el código sin depender constantemente de un compañero

---

## Errores comunes

| Error | Consecuencia | Alternativa |
|-------|--------------|-------------|
| Intentar entenderlo todo en el paso 1 | Parálisis por análisis, frustración | Limitar la exploración a 15 min estrictos |
| Saltar directamente al paso 4 | Código inconsistente con las convenciones | Siempre hacer el paso 3 antes de escribir código |
| No verificar la información del agente | Asumir mapas incorrectos del proyecto | Abrir los archivos clave y confirmar visualmente |
| Hacer un PR demasiado ambicioso | El PR queda bloqueado en review días | Primer PR = cambio mínimo y seguro |

---

## Conexión con otros módulos

- Las técnicas de prompting del [Módulo A1](../../modulo-A1-prompting-efectivo/README.md) son fundamentales para formular las preguntas de exploración
- Si el agente produce información incorrecta durante la exploración, aplica el pensamiento crítico del [Módulo A2](../../modulo-A2-limitaciones-fallos/README.md)
- Para la primera contribución, usa el workflow de debugging del [Módulo A4](../../modulo-A4-debugging-sistematico/README.md) si eliges arreglar un bug
