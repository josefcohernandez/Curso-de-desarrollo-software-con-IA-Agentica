# Ejercicio 4: Debugging cross-stack

## Contexto

Tienes una aplicación fullstack de gestión de proyectos:
- **Frontend**: React 18 con TypeScript
- **Backend**: Express con TypeScript
- **Base de datos**: PostgreSQL 15

Un usuario reporta: "Cuando creo un proyecto con el nombre 'Diseño Q3 — Fase 1', se crea correctamente, pero cuando voy a la lista de proyectos, no aparece. Si creo un proyecto con un nombre más simple como 'Test', sí aparece."

No hay errores visibles en el frontend ni HTTP errors en el network tab.

## El código

### Frontend: Crear proyecto

```typescript
// src/components/CreateProject.tsx
import { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import { createProject } from '../api/projects';

export function CreateProject() {
  const [name, setName] = useState('');
  const [description, setDescription] = useState('');
  const navigate = useNavigate();

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    try {
      const project = await createProject({ name, description });
      navigate(`/projects/${project.id}`);
    } catch (err) {
      alert('Error al crear el proyecto');
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input value={name} onChange={e => setName(e.target.value)} placeholder="Nombre" />
      <textarea value={description} onChange={e => setDescription(e.target.value)} />
      <button type="submit">Crear</button>
    </form>
  );
}
```

### Frontend: Lista de proyectos

```typescript
// src/components/ProjectList.tsx
import { useEffect, useState } from 'react';
import { getProjects } from '../api/projects';
import { Project } from '../types';

export function ProjectList() {
  const [projects, setProjects] = useState<Project[]>([]);
  const [search, setSearch] = useState('');

  useEffect(() => {
    getProjects(search).then(setProjects);
  }, [search]);

  return (
    <div>
      <input
        placeholder="Buscar proyectos..."
        value={search}
        onChange={e => setSearch(e.target.value)}
      />
      <ul>
        {projects.map(p => (
          <li key={p.id}>{p.name} — {p.status}</li>
        ))}
      </ul>
      {projects.length === 0 && <p>No se encontraron proyectos</p>}
    </div>
  );
}
```

### Frontend: API client

```typescript
// src/api/projects.ts
const API_BASE = '/api';

export async function createProject(data: { name: string; description: string }) {
  const res = await fetch(`${API_BASE}/projects`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(data)
  });
  return res.json();
}

export async function getProjects(search?: string) {
  const url = search
    ? `${API_BASE}/projects?search=${search}`
    : `${API_BASE}/projects`;
  const res = await fetch(url);
  return res.json();
}
```

### Backend: Controller

```typescript
// src/controllers/projectController.ts
import { Request, Response } from 'express';
import { ProjectService } from '../services/projectService';

const projectService = new ProjectService();

export async function create(req: Request, res: Response) {
  try {
    const { name, description } = req.body;
    const project = await projectService.create(name, description, req.user.id);
    return res.status(201).json(project);
  } catch (error) {
    console.error('Error creating project:', error);
    return res.status(500).json({ error: 'Error interno' });
  }
}

export async function list(req: Request, res: Response) {
  try {
    const search = req.query.search as string | undefined;
    const userId = req.user.id;
    const projects = await projectService.list(userId, search);
    return res.json(projects);
  } catch (error) {
    console.error('Error listing projects:', error);
    return res.status(500).json({ error: 'Error interno' });
  }
}
```

### Backend: Service

```typescript
// src/services/projectService.ts
import { pool } from '../config/database';

export class ProjectService {
  async create(name: string, description: string, userId: string) {
    const result = await pool.query(
      `INSERT INTO projects (name, description, user_id, status, created_at)
       VALUES ($1, $2, $3, 'active', NOW())
       RETURNING *`,
      [name, description, userId]
    );
    return result.rows[0];
  }

  async list(userId: string, search?: string) {
    let query = 'SELECT * FROM projects WHERE user_id = $1';
    const params: any[] = [userId];

    if (search) {
      query += ` AND name LIKE '%${search}%'`;
    }

    query += ' ORDER BY created_at DESC';

    const result = await pool.query(query, params);
    return result.rows;
  }
}
```

### Base de datos: Schema

```sql
CREATE TABLE projects (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  description TEXT,
  user_id UUID NOT NULL REFERENCES users(id),
  status VARCHAR(50) DEFAULT 'active',
  created_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_projects_user_id ON projects(user_id);
CREATE INDEX idx_projects_name ON projects(name);
```

## Tu tarea

Usa la estrategia de debugging cross-stack de 3 fases:

### Fase 1: Trazar el flujo

Documenta el flujo completo de:
1. Crear un proyecto con nombre "Diseño Q3 -- Fase 1"
2. Listar proyectos (sin filtro de búsqueda)

### Fase 2: Identificar la capa

Para cada capa, indica si hay un problema potencial:

| Capa | ¿Posible problema? | Justificación |
|------|---------------------|---------------|
| Frontend - Crear | | |
| Frontend - Listar | | |
| Backend - Create | | |
| Backend - List | | |
| Base de datos | | |

### Fase 3: Deep dive

- Identifica la causa raíz exacta
- Explica por qué el nombre "Diseño Q3 -- Fase 1" causa el problema pero "Test" no
- Escribe el fix
- Identifica si hay un problema de seguridad adicional en el código

### Bonus: Subagentes

Si estuvieras usando subagentes para este debugging, describe cómo los estructurarías:
- ¿Qué investigaría cada subagente?
- ¿Qué hallazgo del subagente de backend revelaría el bug?

## Criterios de evaluación

| Nivel | Requisito |
|-------|-----------|
| **Aprobado** | Identificar la causa raíz de por qué el proyecto no aparece |
| **Notable** | Todo lo anterior + identificar el problema de seguridad adicional + fix completo |
| **Excelente** | Todo lo anterior + estructurar la investigación con subagentes + test de regresión con caracteres especiales |

---

[← Ejercicio anterior](03-analisis-logs.md) | [Siguiente ejercicio: Post-mortem →](05-post-mortem.md)
