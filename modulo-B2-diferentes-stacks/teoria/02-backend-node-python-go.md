# IA Agéntica y Backend (Node, Python, Go, Java, Rust)

## Panorama por lenguaje

Cada lenguaje backend tiene un perfil diferente cuando trabajas con un agente de código. Esta tabla resume las fortalezas, debilidades y el tip más importante para cada uno:

| Lenguaje | Fortaleza del agente | Debilidad | Tip |
|----------|---------------------|-----------|-----|
| **Python** | Excelente generación de código, bibliotecas bien conocidas (FastAPI, Django, SQLAlchemy) | Puede mezclar paradigmas (sync/async), tipado débil oculta errores | Pedir type hints siempre |
| **Node/TypeScript** | Muy bueno con Express/Fastify, ecosistema NPM bien cubierto | Over-engineering de types, imports circulares entre módulos | Especificar estilo estricto en CLAUDE.md |
| **Go** | Buenos patterns estándar, error handling idiomático, stdlib potente | Puede generar goroutines innecesarias, linters estrictos fallan | Ejecutar `go vet` + `golangci-lint` como hook |
| **Java/Kotlin** | Patterns enterprise bien conocidos (Spring Boot, Hibernate) | Verbosidad excesiva, abstracciones innecesarias (factories de factories) | Pedir código conciso explícitamente |
| **Rust** | Buen manejo del borrow checker, patterns idiomáticos | Puede "luchar" con lifetimes y generar `clone()` excesivos | Revisar ownership cuidadosamente |

## Análisis detallado por lenguaje

### Python

Python es probablemente donde el agente se siente más cómodo. El ecosistema está extremadamente bien documentado y los patterns son predecibles. Pero hay trampas:

- **Mezcla sync/async**: el agente puede usar `requests` (sync) en un proyecto FastAPI (async) sin darse cuenta del conflicto. Especifica siempre: "Usa `httpx` para HTTP requests, todo async."
- **Tipado**: sin instrucciones explícitas, el agente genera código sin type hints. Añade en tu CLAUDE.md: "All Python code must include type hints (PEP 484)."
- **Dependencias**: el agente puede sugerir bibliotecas obsoletas o con vulnerabilidades. Verifica siempre las versiones.

### Node/TypeScript

El agente es muy productivo con TypeScript, pero tiende al over-engineering:

- **Types excesivos**: genera interfaces con 15 campos opcionales cuando bastan 3. Pide: "Keep types minimal — only include fields actually used."
- **Imports circulares**: en proyectos grandes, el agente puede crear dependencias circulares entre módulos. Si ocurre, pídele que reorganice usando un barrel file o dependency injection.
- **Callback hell moderno**: aunque usa async/await, a veces anida promesas innecesariamente. Revisa que el flujo sea lineal.

### Go

Go es interesante porque su simplicidad ayuda al agente:

- **Error handling**: el agente genera el patrón `if err != nil { return err }` correctamente, pero puede olvidar wrapping con `fmt.Errorf("context: %w", err)`.
- **Goroutines**: el agente puede lanzar goroutines donde no hacen falta, creando complejidad innecesaria. Pregunta: "Is a goroutine necessary here, or can this be synchronous?"
- **Linters**: Go tiene linters estrictos. Configura `golangci-lint` como pre-commit hook para capturar problemas automáticamente.

### Java/Kotlin

El agente conoce bien los patterns enterprise, pero puede excederse:

- **Abstracciones innecesarias**: Service → ServiceImpl → AbstractService cuando solo necesitas una clase. Pide: "No interfaces unless there are multiple implementations."
- **Verbosidad**: el agente genera getters, setters, builders y constructores cuando un record (Java 16+) o data class (Kotlin) bastan.
- **Spring Boot**: el agente es muy competente con Spring Boot, pero revisa las anotaciones de seguridad — puede olvidar `@PreAuthorize` o `@Validated`.

### Rust

Rust es el lenguaje más desafiante para el agente:

- **Clone excesivo**: ante errores de borrow checker, el agente recurre a `.clone()` como escape. Cada `.clone()` debería tener justificación.
- **Lifetime annotations**: el agente puede simplificar lifetimes de formas que compilan pero son semánticamente incorrectas.
- **Unsafe**: rara vez lo genera sin que lo pidas, lo cual es bueno. Pero verifica que no haya `unwrap()` en código de producción.

## Patrones comunes backend (independientes del lenguaje)

### 1. API endpoints: OpenAPI-first

El patrón más efectivo para generar APIs es empezar por la especificación:

```markdown
Here is the OpenAPI spec for the /users endpoint (see api/openapi.yaml).
Implement the handlers following the patterns in src/handlers/.
Include input validation, error responses (400, 404, 500), 
and integration tests.
```

Esto funciona mejor que "create a users API" porque el agente tiene un contrato claro contra el que implementar.

### 2. Base de datos: migraciones primero

Siempre genera las migraciones antes del código de acceso a datos:

```markdown
Create a database migration for the orders table:
- id (UUID, primary key)
- user_id (UUID, foreign key to users)
- total (decimal, not null)
- status (enum: pending, confirmed, shipped, delivered)
- created_at, updated_at (timestamps)

Use the migration format in migrations/ directory.
Include both up and down migrations.
```

Después de verificar la migración, pides el código de acceso:

```markdown
Now create the repository/DAL for Orders using the migration 
you just created. Follow the patterns in src/repositories/.
```

### 3. Testing: enfoque híbrido (integration tests + mocks)

La recomendación para backends es un **enfoque híbrido** que combine integration tests reales con mocks donde corresponda. La regla general: **no mockees lo que posees, mockea lo que no posees**.

**Integration tests con base de datos real** — esenciales para lógica de acceso a datos (queries, migraciones, transacciones):

```markdown
Write integration tests for the OrdersRepository.
Use a real test database (SQLite for speed).
Test: create, read, update, delete, list with pagination.
Each test should set up its own data and clean up after.
```

**Mocks para servicios externos** — apropiados para pasarelas de pago, proveedores de email, APIs de terceros y cualquier dependencia que no controles:

```markdown
Write unit tests for the OrderService.
Mock external dependencies: PaymentGateway, EmailProvider, ShippingAPI.
Use real database for repository calls.
Test: order creation flow, payment failure handling, email notification.
```

La razón es práctica: un mock de tu base de datos puede pasar en tests y fallar en producción (queries incorrectas, problemas de migración, race conditions en transacciones). Un mock de Stripe o SendGrid es legítimo porque testear contra el servicio real es lento, costoso e impredecible.

### 4. Documentación: generar desde el código

El agente es excelente generando documentación a partir de código existente:

```markdown
Generate API documentation for the endpoints in src/handlers/.
Format: markdown with examples for each endpoint.
Include: method, path, request body, response body, error codes.
Base examples on the integration tests.
```

## Resumen

| Patrón | Por qué funciona | Riesgo si no lo aplicas |
|--------|-----------------|----------------------|
| OpenAPI-first | El agente tiene un contrato claro | API inconsistente, campos inventados |
| Migraciones primero | Esquema verificado antes del código | Código que no coincide con la DB |
| Testing híbrido | Verifica comportamiento real + aísla dependencias externas | Mocks que pasan pero producción falla, o tests frágiles por dependencias externas |
| Docs desde código | Siempre sincronizadas | Documentación obsoleta desde el día 1 |
