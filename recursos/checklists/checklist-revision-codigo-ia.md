# Checklist de Revisión de Código Generado por IA

## Introducción

Esta checklist expande el Apéndice B del temario con explicaciones detalladas para cada punto. Úsala como referencia durante el code review de cualquier código generado con asistencia de IA. No todos los puntos aplican a cada PR — usa el juicio para priorizar lo relevante.

---

## Sección 1: Scope

Verifica que el agente no hizo más (ni menos) de lo solicitado.

### 1.1. El diff solo toca archivos relacionados con la tarea

**Por qué importa**: el agente tiende a hacer cambios "de paso" en archivos que no le pediste. Estos cambios colaterales pueden introducir regresiones inesperadas.

**Qué buscar**:
- Archivos modificados que no tienen relación con la tarea
- Cambios de formato (whitespace, imports reordenados) en archivos no relacionados
- "Mejoras" no solicitadas en código existente

**Acción**: si hay archivos no relacionados en el diff, pregunta al desarrollador por qué están ahí. Si no hay justificación, revertir esos cambios.

### 1.2. No hay archivos nuevos innecesarios

**Por qué importa**: la IA tiende a crear abstracciones prematuras: archivos de `utils`, `helpers`, `constants` que solo se usan en un lugar.

**Qué buscar**:
- Archivos de utilidades con 1-2 funciones que solo se usan una vez
- Interfaces/types que solo implementa una clase
- Archivos de configuración innecesarios

**Acción**: si un archivo nuevo solo tiene una función usada en un lugar, sugerir inline la función directamente donde se usa.

---

## Sección 2: Corrección

Verifica que el código hace lo que debe hacer.

### 2.1. Los imports existen y son correctos

**Por qué importa**: este es uno de los errores más frecuentes de la IA. El agente genera imports de módulos o funciones que no existen en el proyecto.

**Qué buscar**:
- Imports de paquetes que no están en `package.json` / `requirements.txt`
- Imports de funciones que no existen en el módulo referenciado
- Imports con rutas incorrectas (especialmente rutas relativas)

**Acción**: verificar cada import nuevo. Si el IDE no muestra errores, ejecutar el build para confirmar.

### 2.2. La lógica de negocio es correcta

**Por qué importa**: el agente implementa lógica basada en el prompt, no en el conocimiento del dominio. Si el prompt es ambiguo, la lógica puede ser incorrecta aunque el código "funcione".

**Qué buscar**:
- Condiciones que implementan la lógica al revés
- Reglas de negocio simplificadas (el agente asume happy path)
- Cálculos con errores de redondeo o de tipo de datos

**Acción**: comparar la lógica implementada con los requerimientos. Si no hay requerimientos escritos, preguntarse "¿esto es lo que quería?"

### 2.3. Los edge cases están cubiertos

**Por qué importa**: la IA genera código que funciona para el caso normal pero falla en los bordes: listas vacías, valores null, strings vacíos, números negativos, inputs extremos.

**Qué buscar**:
- ¿Qué pasa si la lista de input está vacía?
- ¿Qué pasa si un campo es null o undefined?
- ¿Qué pasa si el input excede los límites esperados?
- ¿Qué pasa con caracteres especiales en strings?

**Acción**: para cada función nueva, pensar en 2-3 edge cases y verificar si el código los maneja.

### 2.4. No hay regresiones en funcionalidad existente

**Por qué importa**: un cambio en un módulo compartido puede romper funcionalidad en otros módulos.

**Qué buscar**:
- Cambios en funciones usadas por otros módulos
- Cambios en la firma de funciones (parámetros añadidos o eliminados)
- Cambios en el formato de respuesta de APIs

**Acción**: ejecutar la test suite completa, no solo los tests del módulo cambiado.

---

## Sección 3: Seguridad

Verifica que el código no introduce vulnerabilidades.

### 3.1. Sin SQL injection, XSS, path traversal

**Por qué importa**: la IA genera código funcional pero a veces inseguro. Las vulnerabilidades de injection son las más comunes.

**Qué buscar**:
```javascript
// SQL Injection — MAL
const query = `SELECT * FROM users WHERE id = ${userId}`;

// SQL Injection — BIEN
const query = `SELECT * FROM users WHERE id = ?`;
db.query(query, [userId]);
```

```javascript
// XSS — MAL
element.innerHTML = userInput;

// XSS — BIEN
element.textContent = userInput;
```

```javascript
// Path Traversal — MAL
const file = fs.readFileSync(`uploads/${filename}`);

// Path Traversal — BIEN
const safePath = path.resolve('uploads', path.basename(filename));
const file = fs.readFileSync(safePath);
```

### 3.2. Sin secrets hardcodeados

**Qué buscar**:
- API keys, tokens, passwords directamente en el código
- URLs con credenciales embebidas
- Comentarios con información sensible

### 3.3. Sin datos sensibles en logs

**Qué buscar**:
```javascript
// MAL
console.log(`Login attempt: ${email} with password ${password}`);

// BIEN
console.log(`Login attempt: ${email}`);
```

---

## Sección 4: Calidad

Verifica que el código es mantenible y consistente.

### 4.1. Consistente con el estilo del proyecto

**Qué buscar**:
- Naming conventions (camelCase vs snake_case)
- Estructura de archivos (¿sigue el patrón del proyecto?)
- Patrones de error handling (¿usa el mismo estilo que el resto del código?)

### 4.2. Sin abstracciones prematuras

**Por qué importa**: la IA tiende a crear capas de abstracción que no se necesitan todavía (factory patterns, strategy patterns, etc.).

**Regla**: si una abstracción solo tiene una implementación y no hay planes concretos de añadir más, probablemente es prematura.

### 4.3. Sin error handling innecesario

**Por qué importa**: la IA a veces genera try-catch en cada función, creando código que silencia errores en lugar de propagarlos.

**Qué buscar**:
```javascript
// Error handling innecesario — MAL
try {
  const result = simpleCalculation(a, b);
  return result;
} catch (error) {
  console.error("Error in calculation:", error);
  return null;  // Silencia el error
}

// Mejor — dejar que el error propague
const result = simpleCalculation(a, b);
return result;
```

### 4.4. Tests significativos

**Qué buscar**:
```javascript
// Test no significativo — MAL
test('should not throw', () => {
  expect(() => createTask(data)).not.toThrow();
});

// Test significativo — BIEN
test('should create a task with the given title', () => {
  const task = createTask({ title: 'Deploy v2' });
  expect(task.title).toBe('Deploy v2');
  expect(task.status).toBe('pending');
  expect(task.createdAt).toBeInstanceOf(Date);
});
```

---

## Sección 5: Deuda Técnica

Verifica que el código no introduce deuda evitable.

### 5.1. No duplica lógica existente

**Por qué importa**: el agente no siempre conoce todo el codebase. Puede reimplementar funcionalidad que ya existe en otro módulo.

**Acción**: si ves lógica que te suena familiar, busca si ya existe en el proyecto. Si la encuentras, sugerir reutilizar en lugar de duplicar.

### 5.2. Comentarios actualizados

**Qué buscar**:
- Comentarios que describen comportamiento antiguo (el código cambió pero el comentario no)
- JSDoc/docstrings con parámetros que ya no existen
- Comentarios tipo `// TODO: fix this` sin contexto

### 5.3. Sin TODOs olvidados

**Qué buscar**: el agente a veces genera `// TODO` como placeholder y nunca los completa. Si hay un TODO, debe tener:
- Descripción de qué falta
- Ticket/issue asociado
- O ser eliminado si no es realmente necesario

---

## Checklist Rápida (para copiar)

```markdown
## Code Review Checklist

### Scope
- [ ] El diff solo toca archivos relacionados con la tarea
- [ ] No hay archivos nuevos innecesarios (utils, helpers)

### Corrección
- [ ] Los imports existen y son correctos
- [ ] La lógica de negocio es correcta
- [ ] Los edge cases están cubiertos
- [ ] No hay regresiones en funcionalidad existente

### Seguridad
- [ ] Sin SQL injection, XSS, path traversal
- [ ] Sin secrets hardcodeados
- [ ] Sin datos sensibles en logs

### Calidad
- [ ] Consistente con el estilo del proyecto
- [ ] Sin abstracciones prematuras
- [ ] Sin error handling innecesario
- [ ] Tests significativos (no solo "no lanza error")

### Deuda técnica
- [ ] No duplica lógica existente
- [ ] Comentarios actualizados si el código cambió
- [ ] Sin TODOs olvidados
```

---

## Navegación

| | |
|---|---|
| **Recursos** | [Volver a recursos](../README.md) |
