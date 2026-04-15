# Ejercicio 1: Pipeline Multi-Agente Writer/Reviewer para Documentación API

## Objetivo

Diseñar e implementar un pipeline de dos agentes para generar documentación de API: un agente Writer genera la documentación desde el código, y un agente Reviewer verifica que la documentación sea precisa comparándola con la implementación real.

## Contexto

El patrón Writer/Reviewer es uno de los más efectivos para generar contenido de calidad: el Writer tiene la perspectiva del creador, el Reviewer tiene la perspectiva fresca del lector. Aplicado a documentación API, el Writer genera docs desde el código fuente y el Reviewer verifica que los docs reflejan fielmente la implementación.

---

## Instrucciones

### Paso 1: Prepara el código fuente

Usa un proyecto existente que tenga al menos 3 endpoints de API, o crea este ejemplo mínimo:

```typescript
// src/api/users.ts
export async function getUsers(req: Request, res: Response) {
  const { page = 1, limit = 20 } = req.query;
  const users = await db.users.findMany({ skip: (page - 1) * limit, take: limit });
  res.json({ data: users, page, limit, total: await db.users.count() });
}

export async function createUser(req: Request, res: Response) {
  const { name, email, role = "user" } = req.body;
  if (!name || !email) return res.status(400).json({ error: "name and email required" });
  const user = await db.users.create({ data: { name, email, role } });
  res.status(201).json(user);
}

export async function deleteUser(req: Request, res: Response) {
  const { id } = req.params;
  await db.users.delete({ where: { id } });
  res.status(204).send();
}
```

### Paso 2: Diseña el prompt del Writer

Crea un prompt que genere documentación API en formato Markdown:

```text
Lee el código fuente en src/api/ y genera documentación API completa.

Para cada endpoint incluye:
- Método HTTP y ruta
- Parámetros de query/body con tipos
- Respuesta exitosa con ejemplo JSON
- Códigos de error posibles
- Ejemplo de uso con curl

Formato: Markdown con una sección H2 por endpoint.
Guarda el resultado en docs/api-reference.md
```

### Paso 3: Diseña el prompt del Reviewer

Crea un prompt que verifique la documentación contra el código real:

```text
Compara la documentación en docs/api-reference.md con el código fuente en src/api/.

Para cada endpoint documentado, verifica:
1. ¿La ruta y método HTTP son correctos?
2. ¿Los parámetros documentados coinciden con los que acepta el código?
3. ¿Los tipos de respuesta coinciden con lo que devuelve el código?
4. ¿Los códigos de error están todos documentados?
5. ¿Faltan endpoints que existen en el código pero no en la documentación?

Genera un informe en docs/review-report.md con:
- Lista de discrepancias encontradas
- Endpoints faltantes
- Información incorrecta o incompleta
```

### Paso 4: Ejecuta el pipeline

```bash
# Paso 1: Writer genera documentación
claude -p "$(cat prompts/writer-api-docs.txt)" \
  --allowedTools "Read,Glob,Write" \
  --max-turns 15

# Paso 2: Reviewer verifica contra código
claude -p "$(cat prompts/reviewer-api-docs.txt)" \
  --allowedTools "Read,Glob,Write" \
  --max-turns 15

# Paso 3 (opcional): Writer corrige basándose en el review
claude -p "Lee docs/review-report.md y corrige docs/api-reference.md" \
  --allowedTools "Read,Edit" \
  --max-turns 10
```

### Paso 5: Evalúa el resultado

Revisa manualmente:
- ¿La documentación final es precisa?
- ¿El Reviewer encontró errores reales del Writer?
- ¿El ciclo Writer → Reviewer → Corrección mejoró la calidad?

---

## Criterios de Éxito

- [ ] Has creado prompts separados para Writer y Reviewer
- [ ] El Writer genera documentación completa para al menos 3 endpoints
- [ ] El Reviewer identifica al menos 1 discrepancia real
- [ ] El pipeline produce documentación más precisa que un solo agente
- [ ] Los prompts tienen restricciones de herramientas adecuadas

## Tiempo Estimado

15 minutos
