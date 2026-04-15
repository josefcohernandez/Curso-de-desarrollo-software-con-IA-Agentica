# Red flags específicas de código IA

## Las 8 señales de alerta

Estas red flags no son errores de compilación — el código funciona. Pero indican problemas que solo un revisor humano atento puede detectar.

---

### 1. El archivo que aparece de la nada

**Qué es**: el agente crea un nuevo archivo utility, helper o service que no pediste explícitamente.

**Por qué ocurre**: el agente necesita una funcionalidad auxiliar y, en lugar de buscar si ya existe en el proyecto, crea una nueva.

**Ejemplo**:

```bash
# Pediste: "añade validación de email al formulario de registro"
# El agente generó:
src/components/RegisterForm.tsx    (modificado - esperado)
src/utils/validators.ts            (nuevo - ¿por qué?)
src/helpers/stringUtils.ts         (nuevo - ¿realmente necesario?)
```

**Qué hacer**: antes de aceptar, verifica si ya existe lógica equivalente en el proyecto. Pregunta al agente: "¿existe ya algún archivo de validación en el proyecto?"

---

### 2. El try/catch universal

**Qué es**: bloques `try/catch` que capturan todos los errores y los silencian con un `console.log`.

**Por qué ocurre**: el agente quiere que el código "no falle" y envuelve todo en try/catch genéricos.

```javascript
// Red flag: catch que enmascara errores
async function fetchUserData(userId) {
  try {
    const response = await api.get(`/users/${userId}`);
    const profile = await api.get(`/profiles/${userId}`);
    const permissions = await api.get(`/permissions/${userId}`);
    return { ...response.data, ...profile.data, permissions: permissions.data };
  } catch (error) {
    console.log('Error fetching user data:', error);
    return null; // El caller no sabe qué falló ni por qué
  }
}
```

**Lo correcto**: manejar errores específicos, propagar los inesperados, y nunca devolver `null` silenciosamente en una operación crítica.

```javascript
// Mejor: errores específicos y propagación
async function fetchUserData(userId) {
  const response = await api.get(`/users/${userId}`);
  if (!response.data) {
    throw new NotFoundError(`User ${userId} not found`);
  }
  const profile = await api.get(`/profiles/${userId}`);
  const permissions = await api.get(`/permissions/${userId}`);
  return { ...response.data, ...profile.data, permissions: permissions.data };
}
```

---

### 3. La abstracción prematura

**Qué es**: interfaces, factories, clases base o patrones de diseño para un solo caso de uso.

**Por qué ocurre**: los modelos de lenguaje han sido entrenados con mucho código "bien diseñado" y tienden a sobre-abstraer.

```typescript
// Red flag: interface + factory + implementation para UN solo tipo
interface INotificationSender {
  send(message: string, recipient: string): Promise<void>;
}

class NotificationSenderFactory {
  static create(type: 'email'): INotificationSender {
    return new EmailNotificationSender();
  }
}

class EmailNotificationSender implements INotificationSender {
  async send(message: string, recipient: string): Promise<void> {
    await sendEmail(recipient, message);
  }
}
```

**Lo correcto**: una función simple hasta que haya un segundo caso de uso real.

```typescript
// Suficiente por ahora
async function sendEmailNotification(recipient: string, message: string): Promise<void> {
  await sendEmail(recipient, message);
}
```

---

### 4. El test que no testea nada

**Qué es**: tests que existen para cubrir métricas de coverage pero no validan comportamiento real.

```javascript
// Red flag: test sin assertions significativas
test('should process order', async () => {
  const result = await processOrder(mockOrder);
  expect(result).toBeDefined();  // ¿Y qué debería contener?
  expect(result).not.toBeNull(); // Esto no valida nada útil
});

// Red flag: test que solo verifica que "no explota"
test('should handle payment', async () => {
  await expect(handlePayment(validCard)).resolves.not.toThrow();
  // ¿Se cobró la cantidad correcta? ¿Se registró la transacción?
});
```

**Lo correcto**: assertions específicas sobre el comportamiento esperado.

```javascript
test('should process order calculating total with tax', async () => {
  const result = await processOrder({
    items: [{ price: 100, quantity: 2 }],
    taxRate: 0.21
  });
  expect(result.subtotal).toBe(200);
  expect(result.tax).toBe(42);
  expect(result.total).toBe(242);
  expect(result.status).toBe('confirmed');
});
```

---

### 5. El TODO olvidado

**Qué es**: el agente escribe un `// TODO` como marcador y sigue adelante como si el código estuviera completo.

```python
class PaymentProcessor:
    def process(self, payment):
        self.validate(payment)
        result = self.charge(payment)
        # TODO: implement retry logic for failed payments
        # TODO: send confirmation email
        # TODO: update inventory
        return result
```

**Qué hacer**: tratar cada TODO como un requisito incompleto. No aceptar el PR hasta que los TODOs se resuelvan o se conviertan en issues trackeados.

---

### 6. La dependencia fantasma

**Qué es**: el agente importa un paquete que no está en `package.json`, `requirements.txt` u otro manifiesto de dependencias.

```python
# Red flag: ¿está 'dateutil' en requirements.txt?
from dateutil.parser import parse as parse_date
from slugify import slugify  # ¿Y este?
```

```javascript
// Red flag: ¿está 'lodash' en package.json?
import { debounce, throttle } from 'lodash';
```

**Cómo verificar**:

```bash
# Node.js
grep "lodash" package.json

# Python
grep "python-dateutil" requirements.txt
grep "python-slugify" requirements.txt
```

---

### 7. El console.log perdido

**Qué es**: logs de debug que el agente dejó durante el desarrollo y que acabarían en producción.

```javascript
async function createUser(data) {
  console.log('Creating user with data:', data); // Expone datos personales en logs
  console.log('DEBUG: validation passed');        // Log de debug
  const user = await db.users.create(data);
  console.log('User created:', JSON.stringify(user)); // Datos sensibles
  return user;
}
```

**Qué hacer**: buscar `console.log`, `console.debug`, `print()`, `System.out.println` en el diff antes de aprobar. Mejor aún: configurar un linter que lo detecte automáticamente.

```bash
# Buscar logs de debug en el diff
git diff --cached | grep -n "console\.\(log\|debug\|warn\)" | grep "^+"
```

---

### 8. El type assertion agresivo

**Qué es**: el agente fuerza el sistema de tipos en lugar de resolver el problema subyacente de tipado.

```typescript
// Red flags de tipado agresivo
const data = response.body as any;
const user = unknownObject as unknown as User;
const config = JSON.parse(rawConfig) as AppConfig; // Sin validación
```

```python
# Equivalente en Python
result: Any = get_data()  # type: ignore
user = cast(User, unknown_dict)  # Sin validación de estructura
```

**Lo correcto**: validar los datos antes de hacer la aserción.

```typescript
// Validación real en lugar de aserción ciega
import { z } from 'zod';

const UserSchema = z.object({
  id: z.string(),
  email: z.string().email(),
  role: z.enum(['admin', 'user'])
});

const user = UserSchema.parse(response.body); // Lanza error si no cumple
```

---

## Tabla resumen de red flags

| # | Red flag | Señal visible | Riesgo |
|---|----------|---------------|--------|
| 1 | Archivo que aparece de la nada | Archivos nuevos no solicitados en el diff | Duplicación, inconsistencia |
| 2 | Try/catch universal | `catch(e) { console.log(e) }` | Errores enmascarados |
| 3 | Abstracción prematura | Interfaces/factories para un solo uso | Over-engineering |
| 4 | Test vacío | `expect(result).toBeDefined()` | Falsa sensación de cobertura |
| 5 | TODO olvidado | `// TODO: implement...` | Funcionalidad incompleta |
| 6 | Dependencia fantasma | Import de paquete no declarado | Build roto en CI |
| 7 | Console.log perdido | `console.log()` en código de negocio | Fuga de datos, ruido en logs |
| 8 | Type assertion agresivo | `as any`, `as unknown as T` | Errores de runtime ocultos |

---

[← Anterior: Qué buscar](02-que-buscar.md) | [Siguiente: Patrones de diff review →](04-patrones-diff-review.md)
