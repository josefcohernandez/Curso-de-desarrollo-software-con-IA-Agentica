# Ejercicio 4: Red flags en tests

## Contexto

Un agente de código generó un archivo de tests para un servicio de gestión de tareas. Los tests pasan, la cobertura reporta un 85%, pero la suite tiene problemas serios que la hacen poco fiable.

Tu trabajo: identificar todos los problemas y reescribir los tests correctamente.

## El servicio bajo test

```typescript
// src/services/taskService.ts
export class TaskService {
  private db: Database;

  constructor(db: Database) {
    this.db = db;
  }

  async createTask(title: string, assignee: string, dueDate: Date): Promise<Task> {
    if (!title || title.trim().length === 0) throw new ValidationError('Title is required');
    if (!assignee) throw new ValidationError('Assignee is required');
    if (dueDate < new Date()) throw new ValidationError('Due date must be in the future');

    const task: Task = {
      id: generateId(),
      title: title.trim(),
      assignee,
      dueDate,
      status: 'pending',
      createdAt: new Date()
    };

    await this.db.tasks.insert(task);
    return task;
  }

  async completeTask(taskId: string): Promise<Task> {
    const task = await this.db.tasks.findById(taskId);
    if (!task) throw new NotFoundError(`Task ${taskId} not found`);
    if (task.status === 'completed') throw new ConflictError('Task already completed');

    task.status = 'completed';
    task.completedAt = new Date();
    await this.db.tasks.update(task);
    return task;
  }

  async getOverdueTasks(): Promise<Task[]> {
    const tasks = await this.db.tasks.findByStatus('pending');
    return tasks.filter(t => t.dueDate < new Date());
  }
}
```

## Tests generados por el agente (con problemas)

```typescript
// tests/taskService.test.ts
import { TaskService } from '../src/services/taskService';

const mockDb = {
  tasks: {
    insert: jest.fn(),
    findById: jest.fn(),
    findByStatus: jest.fn(),
    update: jest.fn()
  }
};

describe('TaskService', () => {
  let service: TaskService;

  beforeEach(() => {
    service = new TaskService(mockDb as any);
  });

  // Test 1
  test('should create task', async () => {
    const result = await service.createTask('My task', 'Ana', new Date('2099-12-31'));
    expect(result).toBeDefined();
    expect(result).not.toBeNull();
  });

  // Test 2
  test('should create task with correct data', async () => {
    const result = await service.createTask('My task', 'Ana', new Date('2099-12-31'));
    expect(result).toHaveProperty('id');
    expect(result).toHaveProperty('title');
    expect(result).toHaveProperty('status');
  });

  // Test 3
  test('should not create task without title', async () => {
    try {
      await service.createTask('', 'Ana', new Date('2099-12-31'));
    } catch (e) {
      expect(e).toBeDefined();
    }
  });

  // Test 4
  test('should complete task', async () => {
    mockDb.tasks.findById.mockResolvedValue({
      id: '1', title: 'Test', status: 'pending', assignee: 'Ana'
    });
    const result = await service.completeTask('1');
    expect(result).toBeDefined();
    expect(mockDb.tasks.update).toHaveBeenCalled();
  });

  // Test 5
  test('should handle non-existent task', async () => {
    mockDb.tasks.findById.mockResolvedValue(null);
    try {
      await service.completeTask('999');
    } catch (e) {
      // Error is thrown
    }
  });

  // Test 6
  test('should get overdue tasks', async () => {
    mockDb.tasks.findByStatus.mockResolvedValue([
      { id: '1', title: 'Old', dueDate: new Date('2020-01-01'), status: 'pending' },
      { id: '2', title: 'Future', dueDate: new Date('2099-12-31'), status: 'pending' }
    ]);
    const result = await service.getOverdueTasks();
    expect(result).toHaveLength(1);
  });

  // Test 7
  test('should work correctly', async () => {
    const task = await service.createTask('Task 1', 'Bob', new Date('2099-01-01'));
    expect(task).toBeTruthy();
    const task2 = await service.createTask('Task 2', 'Ana', new Date('2099-06-15'));
    expect(task2).toBeTruthy();
  });
});
```

## Tu tarea

### Parte 1: Identificar problemas

Para cada test (1-7), identifica los problemas. Usa esta tabla:

| Test # | Problemas identificados | Gravedad |
|--------|------------------------|----------|
| 1 | | |
| 2 | | |
| 3 | | |
| 4 | | |
| 5 | | |
| 6 | | |
| 7 | | |

### Parte 2: Casos no cubiertos

Lista los casos que deberían testearse y no están cubiertos:
- ¿Qué pasa con los edge cases de validación?
- ¿Qué pasa con tareas ya completadas?
- ¿Se limpia el mock entre tests?

### Parte 3: Reescribir

Reescribe la suite de tests corrigiendo todos los problemas y añadiendo los casos faltantes. Los tests deben:
- Tener assertions específicas sobre valores concretos
- Cubrir los edge cases de cada método
- Verificar el tipo de error lanzado, no solo que "hay un error"
- Ser independientes entre sí (limpiar mocks en `beforeEach`)
- Tener nombres descriptivos que indiquen el comportamiento esperado

## Criterios de evaluación

| Nivel | Requisito |
|-------|-----------|
| **Aprobado** | Identificar al menos 5 problemas y los 3 casos no cubiertos más importantes |
| **Notable** | Identificar todos los problemas y reescribir los tests con assertions específicas |
| **Excelente** | Todo lo anterior + añadir tests para edge cases no obvios (título con solo espacios, fecha exactamente ahora, etc.) |

---

[← Ejercicio anterior](03-poda-complejidad.md) | [Volver al índice del módulo](../README.md)
