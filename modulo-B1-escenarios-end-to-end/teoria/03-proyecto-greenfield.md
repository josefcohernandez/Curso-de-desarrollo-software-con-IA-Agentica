# Escenario: Proyecto Greenfield

## El problema

Empiezas un proyecto desde cero. No hay código, no hay estructura, solo una idea y quizás unos requisitos vagos. Es el momento de máxima libertad y también de máximo riesgo: las decisiones de arquitectura que tomes ahora te acompañarán meses o años.

Un agente de código es un aliado excelente en greenfield porque puede generar estructura rápidamente. Pero también es peligroso: sin una especificación clara, el agente tomará decisiones de arquitectura por ti que luego **costarán mucho cambiar**.

---

## Workflow en 4 pasos

### Paso 1: Entrevista de requerimientos (30 minutos)

El primer paso no es escribir código — es **definir qué vas a construir**. Usa la herramienta `AskUserQuestion` (o equivalente en tu herramienta) para que el agente te entreviste:

```text
I want to build a REST API for managing a library catalog.
Interview me about requirements using structured questions.
Cover:
- Core features (what must it do on day 1?)
- Technical constraints (language, framework, database)
- Target users (developers, end users, internal team?)
- Scalability needs (10 users or 10,000?)
- Integrations (external APIs, auth providers, payment)
- Timeline (MVP in 1 week, 1 month, 3 months?)

Ask me one category at a time. Wait for my answer before moving to the next.
Write the final result to SPEC.md.
```

**Por qué una entrevista y no un prompt largo**: porque tú no sabes todos los requisitos de antemano. El agente hace preguntas que te obligan a pensar en aspectos que habrías olvidado (¿qué pasa si el usuario sube un archivo de 500MB? ¿necesitas paginación?).

**Resultado**: un archivo `SPEC.md` con los requisitos formalizados, que sirve como contrato entre tú y el agente para el resto del proyecto.

### Paso 2: Diseño de arquitectura (20 minutos)

Con la spec en mano, activa Plan Mode (o pide explícitamente que no escriba código todavía):

```text
Based on SPEC.md, design the architecture for this project.
Propose:
- Directory structure with explanation of each folder
- Key modules and their responsibilities
- Data models with fields and relationships
- API endpoints (method, path, description)
- Technology choices with justification for each

Write the result to ARCHITECTURE.md.
Do NOT create any files or code yet — only the design document.
```

**Qué revisar en la arquitectura**:

| Aspecto | Pregunta clave |
|---------|---------------|
| Complejidad | ¿Es proporcional al tamaño del proyecto? Un MVP no necesita microservicios |
| Dependencias | ¿Cada tecnología elegida está justificada? Evitar "por si acaso" |
| Escalabilidad | ¿Puede crecer sin reescribir? No overengineer, pero pensar en extensión |
| Testabilidad | ¿La estructura permite tests unitarios e integración fácilmente? |
| Convenciones | ¿Sigue patrones estándar del ecosistema elegido? |

**Consejo crítico**: dedica tiempo real a revisar `ARCHITECTURE.md`. Es mucho más barato cambiar un documento que reescribir código. Si algo no te convence, discútelo con el agente ahora.

### Paso 3: Scaffolding (15 minutos)

Una vez aprobada la arquitectura, el agente crea la estructura del proyecto:

```text
Create the project structure from ARCHITECTURE.md.
Initialize:
- package.json with the dependencies from the architecture
- tsconfig.json (or equivalent config)
- Test setup (Jest/Vitest config, first test file)
- Linter and formatter config (ESLint, Prettier)
- .gitignore with sensible defaults
- README.md with setup instructions

Create placeholder files for each module with TODO comments
explaining what each module will do.
```

**Qué verificar del scaffolding**:

- [ ] La estructura de directorios coincide con `ARCHITECTURE.md`
- [ ] Las dependencias en `package.json` son las acordadas (no extras)
- [ ] Los archivos de configuración son funcionales (`npm install` funciona)
- [ ] Existe al menos un test que se ejecuta (`npm test` pasa)
- [ ] El `.gitignore` excluye `node_modules/`, `.env`, etc.

### Paso 4: Primera feature con TDD (1 hora)

Ahora sí, código real. Elige la feature más simple y fundamental de la spec:

```text
Let's implement the first feature from SPEC.md: [feature name].
Follow this order:
1. Write the tests first based on the specification
2. Run the tests (they should fail — red phase)
3. Implement the minimum code to pass the tests (green phase)
4. Refactor if needed while keeping tests green

Use the patterns defined in ARCHITECTURE.md.
Run tests after each change.
```

**Ejemplo concreto**: si estás construyendo una API de catálogo de biblioteca, la primera feature podría ser "crear un libro" (POST /api/books con título, autor, ISBN).

**Consejo**: resiste la tentación de implementar múltiples features a la vez. Una feature completa (con tests, validación y error handling) es infinitamente mejor que tres features a medias.

---

## La lección más importante

> **En greenfield, la especificación es todo.**

Sin `SPEC.md`, el agente tomará decisiones de arquitectura que reflejan su training data, no tu contexto. Elegirá frameworks populares en lugar de los adecuados. Añadirá capas de abstracción "por si acaso". Implementará features que no necesitas.

El tiempo invertido en los pasos 1 y 2 se recupera multiplicado en los pasos 3 y 4. Un `SPEC.md` claro reduce las iteraciones de corrección en un 70-80%.

---

## Errores comunes en greenfield

| Error | Consecuencia | Alternativa |
|-------|--------------|-------------|
| Saltar directamente al código | Arquitectura emergente caótica | Siempre spec + architecture antes de escribir código |
| Aceptar la primera arquitectura que propone el agente | Over-engineering o tecnologías inadecuadas | Pedir 2-3 alternativas y evaluar pros/contras |
| Intentar implementar todo en una sesión | Fatiga de supervisión, calidad decreciente | Una feature a la vez, `/clear` entre features |
| No escribir tests desde el inicio | Deuda técnica desde el día 1 | TDD desde la primera feature |
| Spec demasiado vaga ("una app de tareas") | El agente llena los huecos con asunciones | Responder las preguntas de la entrevista con detalle |

---

## Artefactos del proceso

Al finalizar el workflow greenfield, deberías tener:

1. **SPEC.md**: requisitos formalizados con features, restricciones y criterios de éxito
2. **ARCHITECTURE.md**: diseño técnico con estructura, modelos, endpoints y justificaciones
3. **Proyecto scaffolded**: estructura de directorios, configuración y dependencias listas
4. **Primera feature funcional**: con tests, implementación y documentación mínima
5. **Primer commit**: todo lo anterior bajo control de versiones

---

## Conexión con otros módulos

- La entrevista de requerimientos usa las técnicas de prompting iterativo del [Módulo A1](../../modulo-A1-prompting-efectivo/README.md)
- La revisión de la arquitectura propuesta aplica el pensamiento crítico del [Módulo A2](../../modulo-A2-limitaciones-fallos/README.md)
