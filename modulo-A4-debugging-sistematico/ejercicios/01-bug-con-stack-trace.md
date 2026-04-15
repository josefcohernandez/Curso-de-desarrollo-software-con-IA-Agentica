# Ejercicio 1: Bug con stack trace

## Contexto

Tienes una aplicación Express que gestiona tareas. Un usuario reporta que al intentar marcar una tarea como completada, recibe un error 500. Tienes el stack trace y los pasos para reproducir.

## Información del bug

### Error reportado

```text
POST /api/tasks/complete - 500 Internal Server Error

TypeError: Cannot read properties of null (reading 'status')
    at TaskService.completeTask (src/services/taskService.ts:42:19)
    at TaskController.complete (src/controllers/taskController.ts:28:35)
    at Layer.handle [as handle_request] (node_modules/express/lib/router/layer.js:95:5)
    at next (node_modules/express/lib/router/route.js:144:13)
    at AuthMiddleware.verify (src/middleware/auth.ts:15:5)
    at Layer.handle [as handle_request] (node_modules/express/lib/router/layer.js:95:5)
```

### Pasos para reproducir

1. Login como usuario `ana@ejemplo.com`
2. Ir a "Mis tareas"
3. Click en "Completar" en la tarea "Preparar presentación"
4. Error 500 en la pantalla

### Entorno

- Node.js 20, Express 4.18, TypeScript 5.3
- PostgreSQL 15
- El bug se reproduce siempre con esa tarea específica, pero NO con otras tareas

## El código relevante

### `src/controllers/taskController.ts`

```typescript
import { Request, Response } from 'express';
import { TaskService } from '../services/taskService';

export class TaskController {
  private taskService: TaskService;

  constructor(taskService: TaskService) {
    this.taskService = taskService;
  }

  async complete(req: Request, res: Response) {
    try {
      const { taskId } = req.body;
      const userId = req.user.id;

      const task = await this.taskService.completeTask(taskId, userId);

      return res.json({
        message: 'Tarea completada',
        task
      });
    } catch (error) {
      console.log('Error completing task:', error);
      return res.status(500).json({ error: 'Error interno del servidor' });
    }
  }
}
```

### `src/services/taskService.ts`

```typescript
import { TaskRepository } from '../repositories/taskRepository';

export class TaskService {
  private taskRepo: TaskRepository;

  constructor(taskRepo: TaskRepository) {
    this.taskRepo = taskRepo;
  }

  async completeTask(taskId: string, userId: string) {
    // Verify the task belongs to the user
    const task = await this.taskRepo.findByIdAndUser(taskId, userId);

    // Line 42: the error happens here
    if (task.status === 'completed') {
      throw new Error('La tarea ya está completada');
    }

    task.status = 'completed';
    task.completedAt = new Date();

    await this.taskRepo.update(task);
    return task;
  }
}
```

### `src/repositories/taskRepository.ts`

```typescript
import { Pool } from 'pg';

export class TaskRepository {
  private pool: Pool;

  constructor(pool: Pool) {
    this.pool = pool;
  }

  async findByIdAndUser(taskId: string, userId: string) {
    const result = await this.pool.query(
      'SELECT * FROM tasks WHERE id = $1 AND user_id = $2',
      [taskId, userId]
    );
    return result.rows[0]; // Returns undefined if not found
  }

  async update(task: any) {
    await this.pool.query(
      'UPDATE tasks SET status = $1, completed_at = $2 WHERE id = $3',
      [task.status, task.completedAt, task.id]
    );
  }
}
```

### Datos adicionales

Consultando la base de datos:

```sql
SELECT id, title, user_id, status FROM tasks WHERE title = 'Preparar presentación';
```

```text
 id  |        title          | user_id | status
-----+-----------------------+---------+---------
 t47 | Preparar presentación | u12     | pending
```

```sql
SELECT id, email FROM users WHERE email = 'ana@ejemplo.com';
```

```text
 id  |      email
-----+------------------
 u15 | ana@ejemplo.com
```

## Tu tarea

Sigue el workflow de 4 pasos del módulo:

### Paso 1: Reproducir
- ¿Tienes toda la información necesaria? ¿Qué falta?
- Resume el error en tus propias palabras

### Paso 2: Aislar
- ¿Qué archivos están involucrados?
- Formula 2-3 hipótesis sobre la causa raíz
- ¿Por qué el bug solo ocurre con esta tarea y no con otras?

### Paso 3: Diagnosticar
- Verifica cada hipótesis con la información disponible
- ¿Cuál es la causa raíz confirmada?

### Paso 4: Fix + Validar
- Escribe el fix
- Escribe un test de regresión que falle sin el fix y pase con él
- ¿Hay otros lugares del código con el mismo patrón vulnerable?

## Criterios de evaluación

| Nivel | Requisito |
|-------|-----------|
| **Aprobado** | Identificar la causa raíz y proponer un fix |
| **Notable** | Seguir los 4 pasos documentando cada uno, incluir test de regresión |
| **Excelente** | Todo lo anterior + identificar el patrón vulnerable en el repositorio y proponer un fix sistémico |

---

[Siguiente ejercicio: Bug sin stack trace →](02-bug-sin-stack-trace.md)
