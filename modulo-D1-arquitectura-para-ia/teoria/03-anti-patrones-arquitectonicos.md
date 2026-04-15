# Anti-Patrones Arquitectónicos que Confunden a los Agentes

## Introducción

Los anti-patrones de esta sección no son código "malo" en el sentido tradicional — mucho de este código funciona perfectamente. El problema es que confunde a los agentes de código, provocando alucinaciones, cambios incorrectos y pérdida de tiempo. Reconocer estos anti-patrones es el primer paso para eliminarlos.

---

## Anti-Patrón 1: Convenciones Implícitas

### El problema

"Todos saben que los archivos en `/utils` no se tocan directamente" — el agente no lo sabe.

Las convenciones implícitas son reglas que el equipo conoce pero que no están documentadas en ningún lugar que el agente pueda leer. Incluyen:

- Orden esperado de imports
- Archivos que "no se deben tocar" directamente
- Patrones de naming que nadie ha escrito
- Flujos de trabajo asumidos ("siempre ejecutar migrations antes de tests")

### Ejemplo del problema

```text
Prompt: "Añade una función de formateo de fechas al proyecto"

Lo que hace el agente: Crea un archivo nuevo `src/utils/date-formatter.ts`

Lo que esperaba el equipo: Que lo añadiera a `src/shared/helpers/formatters.ts`
porque "todos saben que las utilidades van en shared/helpers"
```

### Solución

Documentar en CLAUDE.md o en `.claude/rules/`:

```markdown
## Estructura del código
- Utilidades compartidas van en `src/shared/helpers/` — nunca crear archivos en `src/utils/`
- `src/utils/` es legacy y está en proceso de migración — no añadir nada nuevo ahí
- Nuevas funciones de formateo van en `src/shared/helpers/formatters.ts`
```

### Señales de este anti-patrón

- El agente crea archivos en ubicaciones inesperadas
- El agente usa un patrón de naming diferente al del equipo
- Hay code review frecuente de "eso no va ahí"

---

## Anti-Patrón 2: Magia y Metaprogramación

### El problema

Decoradores que cambian el comportamiento, monkey patching, dynamic imports, proxies y metaprogramación hacen que el agente genere código que no funciona porque no puede "ver" la magia.

### Ejemplo del problema

```python
# Un decorador que registra automáticamente la ruta en el router
@api_route("/users/{id}")
@require_auth(roles=["admin"])
@cache(ttl=300)
@rate_limit(100, per="minute")
def get_user(id: str):
    return db.users.find(id)
```

El agente ve la función `get_user` pero puede no entender que:
- Está registrada automáticamente en un router
- Requiere autenticación con rol "admin"
- Tiene caché de 5 minutos
- Tiene rate limiting

Si el agente crea un nuevo endpoint, puede olvidar los decoradores o usarlos en orden incorrecto.

### Otro ejemplo: monkey patching

```javascript
// En algún archivo de inicialización...
Date.prototype.toBusinessDay = function() {
  // Lógica personalizada
};

// En otro archivo, sin import visible...
const nextDay = someDate.toBusinessDay();
// El agente no entiende de dónde viene .toBusinessDay()
```

### Solución

Preferir código explícito sobre clever code:

```python
# Explícito: el agente ve exactamente qué está pasando
router.add_route("/users/{id}", get_user, methods=["GET"])
router.add_middleware(get_user, [auth_middleware(roles=["admin"])])
router.add_middleware(get_user, [cache_middleware(ttl=300)])
router.add_middleware(get_user, [rate_limit_middleware(100, per="minute")])
```

Si la metaprogramación es necesaria (frameworks, DSLs internos), documentarla explícitamente:

```markdown
## Decoradores de API (ver `.claude/rules/api-decorators.md`)
- `@api_route(path)`: registra la función como endpoint en el router
- `@require_auth(roles)`: añade middleware de autenticación
- Orden obligatorio: @api_route → @require_auth → @cache → @rate_limit
```

---

## Anti-Patrón 3: Acoplamiento Temporal

### El problema

"Este módulo funciona solo si primero ejecutas el seed de la base de datos." El agente no sabe el orden de ejecución, genera código que compila pero falla en runtime.

### Ejemplo del problema

```javascript
// test-setup.js — se ejecuta antes de los tests, pero no lo dice en ningún lado
await seedDatabase();
await initializeCache();
await loadFeatureFlags();

// user.service.test.js — el agente no sabe que depende de test-setup.js
describe("UserService", () => {
  it("should find user by email", async () => {
    // Este test falla si el seed no se ejecutó primero
    const user = await userService.findByEmail("test@example.com");
    expect(user).toBeDefined();
  });
});
```

Si el agente intenta ejecutar un test individual, falla. Si crea un nuevo test, puede no incluir la dependencia.

### Solución

Hacer las dependencias temporales explícitas:

```javascript
// Opción A: cada test tiene su propio setup
describe("UserService", () => {
  beforeEach(async () => {
    await seedTestUsers(); // Explícito: este test necesita datos
  });

  afterEach(async () => {
    await cleanupTestUsers();
  });

  it("should find user by email", async () => {
    const user = await userService.findByEmail("test@example.com");
    expect(user).toBeDefined();
  });
});

// Opción B: checks en startup
function initializePaymentModule() {
  if (!config.stripe.apiKey) {
    throw new Error("STRIPE_API_KEY is required. Run `cp .env.example .env` first.");
  }
  if (!db.isConnected()) {
    throw new Error("Database connection required. Run `npm run db:start` first.");
  }
}
```

### Señales de este anti-patrón

- Tests que pasan en CI pero fallan localmente (o viceversa)
- El agente genera tests que fallan con errores de "not found" o "connection refused"
- Documentación que dice "primero ejecuta X, luego Y, luego Z"

---

## Anti-Patrón 4: Tests Acoplados al Orden de Ejecución

### El problema

Tests que dependen de que otro test se ejecute primero. El agente modifica un test y rompe 5 que dependían de su efecto secundario.

### Ejemplo del problema

```javascript
// Estos tests dependen del orden de ejecución
describe("UserCRUD", () => {
  let userId;

  it("should create a user", async () => {
    const user = await createUser({ name: "Alice" });
    userId = user.id; // ← El siguiente test depende de este
  });

  it("should find the created user", async () => {
    const user = await findUser(userId); // ← Falla si el test anterior no se ejecutó
    expect(user.name).toBe("Alice");
  });

  it("should delete the user", async () => {
    await deleteUser(userId); // ← Falla si los dos anteriores no se ejecutaron
  });
});
```

Si el agente reordena, elimina o modifica el primer test, los demás se rompen sin razón obvia.

### Solución

Tests independientes con setup y teardown propios:

```javascript
describe("UserCRUD", () => {
  it("should create a user", async () => {
    const user = await createUser({ name: "Alice" });
    expect(user.id).toBeDefined();
    // Cleanup
    await deleteUser(user.id);
  });

  it("should find an existing user", async () => {
    // Setup propio
    const created = await createUser({ name: "Bob" });
    const found = await findUser(created.id);
    expect(found.name).toBe("Bob");
    // Cleanup propio
    await deleteUser(created.id);
  });

  it("should delete a user", async () => {
    // Setup propio
    const user = await createUser({ name: "Charlie" });
    await deleteUser(user.id);
    const found = await findUser(user.id);
    expect(found).toBeNull();
  });
});
```

---

## Resumen: Tabla de Anti-Patrones

| Anti-patrón | Síntoma con IA | Solución |
|-------------|----------------|----------|
| Convenciones implícitas | Agente crea archivos en ubicaciones incorrectas | Documentar en CLAUDE.md o `.claude/rules/` |
| Magia y metaprogramación | Agente genera código que ignora decoradores/proxies | Código explícito o documentación detallada de la magia |
| Acoplamiento temporal | Tests/código fallan en runtime sin error claro | Setup explícito, checks en startup |
| Tests acoplados al orden | Modificar un test rompe otros sin relación obvia | Tests independientes con setup/teardown propio |

La regla general: **si un humano nuevo necesitaría que le expliquen algo verbalmente, un agente lo necesita por escrito**.
