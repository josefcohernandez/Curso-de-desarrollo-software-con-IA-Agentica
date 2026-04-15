# Ejercicio 2: Bug sin stack trace

## Contexto

Tienes una aplicación web de gestión de inventario. Un usuario del equipo de almacén reporta: "Cuando actualizo el stock de un producto, a veces el número que se muestra después no es el que yo puse."

No hay error visible. No hay stack trace. El usuario dice que "a veces pasa y a veces no". Tu trabajo es investigar, aislar y resolver.

## Información disponible

### Reporte del usuario

> "Pongo que hay 50 unidades del producto 'Tornillo M8', le doy a guardar, y cuando recargo la página a veces dice 50 y a veces dice otro número, como 47 o 53. Pero yo seguro que puse 50."

### Lo que sabes del sistema

- Frontend: React 18 con React Query para data fetching
- Backend: Express con PostgreSQL
- La app la usan 3 personas del equipo de almacén simultáneamente
- No hay errores en la consola del navegador
- No hay errores en los logs del servidor (todos los requests devuelven 200)

### Código del frontend

```typescript
// src/components/StockEditor.tsx
import { useMutation, useQueryClient } from '@tanstack/react-query';
import { updateStock } from '../api/inventory';

export function StockEditor({ productId, currentStock }) {
  const [quantity, setQuantity] = useState(currentStock);
  const queryClient = useQueryClient();

  const mutation = useMutation({
    mutationFn: (newStock: number) => updateStock(productId, newStock),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['products'] });
    }
  });

  const handleSave = () => {
    mutation.mutate(quantity);
  };

  return (
    <div>
      <input
        type="number"
        value={quantity}
        onChange={(e) => setQuantity(Number(e.target.value))}
      />
      <button onClick={handleSave} disabled={mutation.isPending}>
        Guardar
      </button>
      {mutation.isSuccess && <span>Guardado correctamente</span>}
    </div>
  );
}
```

### Código de la API client

```typescript
// src/api/inventory.ts
export async function updateStock(productId: string, newStock: number) {
  const response = await fetch(`/api/products/${productId}/stock`, {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ stock: newStock })
  });
  return response.json();
}
```

### Código del backend

```typescript
// src/controllers/inventoryController.ts
import { Request, Response } from 'express';
import { pool } from '../config/database';

export async function updateStock(req: Request, res: Response) {
  const { productId } = req.params;
  const { stock } = req.body;

  await pool.query(
    'UPDATE products SET stock = stock + $1 WHERE id = $2',
    [stock, productId]
  );

  const result = await pool.query(
    'SELECT * FROM products WHERE id = $1',
    [productId]
  );

  return res.json(result.rows[0]);
}
```

### Datos de la base de datos

```sql
SELECT id, name, stock FROM products WHERE name = 'Tornillo M8';
```

```text
  id  |    name      | stock
------+--------------+-------
 p123 | Tornillo M8  |   153
```

## Tu tarea

### Paso 1: Observar

No hay stack trace, así que empieza observando:
- ¿Qué síntomas se describen exactamente?
- ¿Hay un patrón? ("a veces" sugiere un problema intermitente)
- ¿Qué variables podrían causar inconsistencia?

### Paso 2: Formular hipótesis

Genera al menos 3 hipótesis. Piensa en:
- ¿Qué podría hacer que el stock "cambie" sin que el usuario lo note?
- ¿Qué pasa cuando 3 personas usan la app simultáneamente?
- ¿Hay alguna diferencia entre lo que el frontend envía y lo que el backend hace?

### Paso 3: Verificar

Para cada hipótesis, indica:
- ¿Qué buscarías en el código para confirmarla o descartarla?
- ¿Qué log o query añadirías para obtener más evidencia?

### Paso 4: Resolver

- ¿Cuál es la causa raíz?
- Escribe el fix (puede requerir cambios en frontend y/o backend)
- ¿Qué test escribirías para prevenir esta regresión?

## Pistas

Si te atascas, considera estas pistas en orden:

1. Lee con atención la query SQL del backend. ¿Hace lo que crees que hace?
2. ¿Qué pasa si dos usuarios actualizan el stock del mismo producto al mismo tiempo?
3. Compara lo que el frontend envía (`{ stock: 50 }`) con lo que el backend interpreta

## Criterios de evaluación

| Nivel | Requisito |
|-------|-----------|
| **Aprobado** | Identificar la causa raíz del problema |
| **Notable** | Identificar la causa raíz, explicar por qué es intermitente, proponer fix |
| **Excelente** | Todo lo anterior + considerar concurrencia, proponer fix robusto y test |

---

[← Ejercicio anterior](01-bug-con-stack-trace.md) | [Siguiente ejercicio: Análisis de logs →](03-analisis-logs.md)
