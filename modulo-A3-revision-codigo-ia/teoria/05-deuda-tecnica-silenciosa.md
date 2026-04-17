# Deuda técnica silenciosa

## Qué es

La deuda técnica silenciosa es código que **funciona hoy** pero acumula problemas para el futuro — y que nadie cuestiona porque "el agente lo generó y funciona".

A diferencia de la deuda técnica tradicional (que suele ser consciente: "sé que esto es un hack, pero tenemos deadline"), la deuda silenciosa se introduce **sin que nadie la note**. El agente genera código funcional, los tests pasan, y el equipo avanza. Los problemas aparecen semanas o meses después.

## Las 4 formas comunes

### 1. Duplicación sutil

**Qué es**: el agente reimplementa lógica que ya existe en otro módulo porque no la encontró en el contexto.

```javascript
// En src/services/orderService.ts (nuevo, generado por IA)
function formatCurrency(amount, currency = 'EUR') {
  return new Intl.NumberFormat('es-ES', {
    style: 'currency',
    currency
  }).format(amount);
}

// Ya existía en src/utils/formatting.ts (no estaba en el contexto del agente)
export function formatPrice(amount, currencyCode = 'EUR') {
  return new Intl.NumberFormat('es-ES', {
    style: 'currency',
    currency: currencyCode
  }).format(amount);
}
```

**El problema**: ahora tienes dos funciones que hacen lo mismo. Cuando cambies el formato (por ejemplo, para soportar otro locale), tendrás que encontrar y modificar ambas — y probablemente olvidarás una.

**Cómo detectarlo**: cuando revises código nuevo, pregúntate: "¿ya tenemos algo que haga esto?" Usa búsquedas en el proyecto:

```bash
# Buscar funciones similares en el proyecto
grep -rn "formatCurrency\|formatPrice\|formatMoney" src/
```

### 2. Abstracciones inconsistentes

**Qué es**: en un módulo el agente usa un patrón, en otro usa un patrón diferente para resolver el mismo tipo de problema.

```typescript
// En el módulo de usuarios: patrón Repository
class UserRepository {
  async findById(id: string): Promise<User> { /* ... */ }
  async save(user: User): Promise<void> { /* ... */ }
}

// En el módulo de productos (generado en otra sesión): acceso directo
async function getProduct(id: string): Promise<Product> {
  return await db.query('SELECT * FROM products WHERE id = $1', [id]);
}

async function saveProduct(product: Product): Promise<void> {
  await db.query('INSERT INTO products ...', [product.name, product.price]);
}
```

**El problema**: un nuevo desarrollador no sabe qué patrón seguir. La base de código se vuelve impredecible. El refactoring se hace cada vez más costoso.

### 3. Tests frágiles

**Qué es**: tests que están acoplados a la implementación en lugar de al comportamiento.

```javascript
// Test frágil: acoplado a la implementación interna
test('should save user', async () => {
  const saveSpy = jest.spyOn(db, 'query');
  await createUser({ name: 'Ana', email: 'ana@test.com' });
  
  expect(saveSpy).toHaveBeenCalledWith(
    'INSERT INTO users (name, email) VALUES ($1, $2) RETURNING *',
    ['Ana', 'ana@test.com']
  );
});
// Si cambias la query SQL (mismo resultado, diferente formato), el test falla
```

```javascript
// Test robusto: verifica el comportamiento, no la implementación
test('should save user and return it with an id', async () => {
  const user = await createUser({ name: 'Ana', email: 'ana@test.com' });
  
  expect(user.id).toBeDefined();
  expect(user.name).toBe('Ana');
  expect(user.email).toBe('ana@test.com');
  
  // Verificar que realmente se guardó
  const saved = await getUser(user.id);
  expect(saved.name).toBe('Ana');
});
```

### 4. Documentación ghost

**Qué es**: comentarios que describían el código original pero que ahora describen código que ya cambió.

```python
# Calcula el precio con descuento del 10% para clientes premium
# (Nota: el descuento se actualizo al 15% hace 3 meses, pero el comentario no)
def calculate_price(base_price, is_premium):
    discount = 0.15 if is_premium else 0
    return base_price * (1 - discount)
```

**El problema**: los comentarios desactualizados son peor que no tener comentarios — desinforman activamente.

## Cómo combatir la deuda técnica silenciosa

### 1. Revisiones periódicas

No revises solo en el momento de generación. Programa revisiones periódicas del código generado por IA:

| Frecuencia | Qué revisar | Objetivo |
|------------|-------------|----------|
| Semanal | Archivos nuevos creados por IA | Detectar duplicación y archivos innecesarios |
| Quincenal | Patrones de acceso a datos, error handling | Detectar inconsistencias entre módulos |
| Mensual | Suite de tests completa | Detectar tests frágiles y coverage falso |

### 2. Mantener actualizado el archivo de instrucciones

Un archivo de instrucciones bien mantenido (`AGENTS.md`, `CLAUDE.md` o equivalente) reduce la deuda técnica porque el agente conoce las convenciones:

```markdown
## Patrones del proyecto

- Acceso a datos: usar el patrón Repository (ver src/repositories/)
- Formato de moneda: usar `formatPrice()` de `src/utils/formatting.ts`
- Error handling: lanzar excepciones tipadas (NotFoundError, ValidationError)
- No crear archivos utils nuevos sin verificar los existentes
```

### 3. La regla de las 100 líneas

> Si el agente genera **más de 100 líneas**, reserva **10 minutos** para revisión humana.

Esta regla simple pero efectiva establece un umbral de atención. No significa que 99 líneas están libres de revisión — pero reconoce que los cambios grandes son donde más se acumula la deuda silenciosa.

### 4. Preguntas de cierre

Antes de aceptar cualquier PR generado por IA, hazte estas 3 preguntas:

1. **¿Existe ya algo que haga esto?** → Busca duplicación
2. **¿Es consistente con cómo lo hacemos en otros módulos?** → Busca inconsistencia
3. **¿Los tests verifican comportamiento o implementación?** → Busca fragilidad

Si no puedes responder "sí, sí, comportamiento" — el PR necesita trabajo.

---

[← Anterior: Patrones de diff review](04-patrones-diff-review.md) | [Volver al índice del módulo](../README.md)
