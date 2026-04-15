# Debugging cross-stack

## El desafío

Los bugs más difíciles de resolver son los que **cruzan capas**: una acción del usuario en el frontend falla, pero la causa puede estar en el API controller, en la lógica de negocio, en la query de base de datos o en la configuración de un servicio externo.

El debugging cross-stack requiere entender cómo fluyen los datos a través de todo el sistema. Es aquí donde los agentes de código brillan — pueden leer código de frontend, backend y configuración de infraestructura en la misma sesión.

## La estrategia de 3 fases

### Fase 1: Trazar el request de principio a fin

Antes de buscar el bug, necesitas un mapa del flujo completo.

```text
Traza lo que ocurre cuando un usuario hace click en "Guardar pedido"
desde el componente React hasta la base de datos.

Muéstrame la cadena completa:
1. Componente → función/handler que se ejecuta
2. Llamada HTTP → URL, método, payload
3. Controller/route que recibe la petición
4. Service/lógica de negocio que se ejecuta
5. Queries a base de datos
6. Transformaciones de datos en el camino de vuelta

No busques bugs todavía, solo muéstrame el flujo.
```

### Fase 2: Identificar la capa que falla

Una vez tienes el mapa, identifica dónde se produce el fallo.

```text
El flujo que acabas de trazar falla con este error:
[pegar error]

El frontend recibe un HTTP 500. Necesito saber:
- ¿El error se produce en el controller, el service, el repository o la base de datos?
- ¿El request llega correctamente al backend? (verifica que el payload es correcto)
- ¿La respuesta de error incluye información útil o es genérica?
```

**Guía de diagnóstico por síntoma:**

| Síntoma | Capa probable | Verificación |
|---------|---------------|-------------|
| HTTP 400 Bad Request | Frontend (payload incorrecto) o Controller (validación) | Comparar payload enviado vs esquema esperado |
| HTTP 401/403 | Middleware de auth | Verificar token/sesión y permisos |
| HTTP 404 | Routing o recurso inexistente | Verificar URL y que el registro existe en la DB |
| HTTP 500 genérico | Service o Repository | Revisar logs del servidor para el stack trace real |
| Timeout | DB query lenta o servicio externo | Verificar tiempos de query y conectividad |
| Datos incorrectos (HTTP 200) | Transformación de datos | Comparar input vs output en cada capa |

### Fase 3: Deep dive en la capa problemática

Una vez identificada la capa, haz un análisis profundo solo en esa capa.

```text
El problema está en [nombre del service/controller/repository].
Analiza este archivo en profundidad:
1. ¿La lógica maneja correctamente el caso donde [condición del bug]?
2. ¿Hay algún estado o variable que pueda ser null/undefined en este punto?
3. ¿Los tipos de datos coinciden entre lo que recibe y lo que esperan sus dependencias?
```

## Usar subagentes para investigación paralela

En bugs cross-stack complejos, puedes lanzar investigaciones simultáneas en diferentes capas.

### Cuándo usar subagentes

- El bug podría estar en cualquiera de 3+ capas
- Quieres ahorrar tiempo investigando en paralelo
- Cada capa tiene suficiente código como para justificar una investigación independiente

### Cómo estructurarlo

```text
Tengo un bug cross-stack. Necesito investigar 3 capas en paralelo.

Subagente 1 — Frontend:
Investiga el componente OrderForm y el hook useCreateOrder.
¿El payload que se envía al backend es correcto?
¿Se manejan correctamente los estados de loading y error?

Subagente 2 — Backend:
Investiga el endpoint POST /api/orders (controller + service).
¿La validación es correcta? ¿El service transforma bien los datos?
¿Los errores se propagan correctamente al controller?

Subagente 3 — Base de datos:
Investiga las queries del orderRepository.
¿Las queries usan parámetros correctos?
¿Los constraints de la tabla podrían causar fallos silenciosos?
¿Hay índices faltantes que causen timeouts?
```

### Consolidar hallazgos

Después de que los subagentes reporten:

```text
Resultados de la investigación paralela:
- Frontend: [resumen del hallazgo]
- Backend: [resumen del hallazgo]
- Base de datos: [resumen del hallazgo]

Con esta información, identifica la causa raíz.
¿Hay alguna interacción entre capas que los subagentes 
no pudieron ver individualmente?
```

## Ejemplo completo: bug cross-stack

### Escenario

Un usuario reporta: "Al crear un pedido con un cupón de descuento, el total se muestra correctamente en la pantalla de checkout pero el cobro es por el precio completo."

### Investigación por capas

**Frontend** (subagente 1):
- El componente calcula `total = subtotal - discount` correctamente
- Envía al backend: `{ items: [...], couponCode: 'SAVE20' }`
- El couponCode sí se incluye en el payload

**Backend** (subagente 2):
- El controller recibe el couponCode
- El orderService valida el cupón y calcula el descuento
- Pero el paymentService recibe `createPayment(order.total)` donde `order.total` se calcula **antes** de aplicar el descuento

**Base de datos** (subagente 3):
- La tabla `orders` tiene columnas `subtotal`, `discount`, `total`
- El total se guarda correctamente con el descuento aplicado
- Pero la tabla `payments` registra el `amount` que le pasa el paymentService

### Causa raíz

El bug está en la **secuencia de operaciones** del orderService: primero se calcula `order.total`, luego se crea el pago con ese total, y solo después se aplica el descuento y se actualiza el total de la order. El payment ya se creó con el total sin descuento.

Ningún subagente individual habría encontrado esto — el bug está en la **interacción entre el flujo de order y el flujo de payment**.

---

[← Anterior: Análisis de logs](03-analisis-logs.md) | [Siguiente: Post-mortems asistidos →](05-post-mortems.md)
