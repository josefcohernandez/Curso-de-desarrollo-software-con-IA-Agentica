# Alucinaciones de Código — Detectar y Prevenir

## Qué Son

Una alucinación de código ocurre cuando el agente genera código que referencia APIs, funciones, imports o comportamientos que **no existen en la realidad**. El agente no "sabe" que está inventando — produce el código con la misma confianza que si fuera correcto.

A diferencia de las alucinaciones en texto, las alucinaciones de código tienen una ventaja: **son verificables objetivamente**. Un import existe o no existe. Una función acepta ciertos parámetros o no. El compilador y los tests son tu aliado.

---

## Ejemplos Comunes

### 1. Imports de módulos inexistentes

```javascript
// El agente genera con total confianza:
import { validateSchema } from "express-validator/schema";
// El módulo "express-validator/schema" no existe
// Lo correcto: import { checkSchema } from "express-validator"
```

### 2. Métodos inventados sobre librerías reales

```python
import pandas as pd

df = pd.read_csv("data.csv")
cleaned = df.auto_clean()  # Este método NO existe en pandas
# Lo correcto: implementar la limpieza manualmente o usar otra librería
```

### 3. Parámetros de función inventados

```javascript
const response = await fetch("/api/data", {
  method: "GET",
  retry: 3,        // NO EXISTE — fetch no tiene opción retry nativa
  timeout: 5000    // NO EXISTE — fetch no tiene timeout directo
});

// Para timeout lo correcto es usar AbortController:
const controller = new AbortController();
setTimeout(() => controller.abort(), 5000);
const response = await fetch("/api/data", { signal: controller.signal });
```

### 4. Opciones de configuración inventadas

```yaml
# docker-compose.yml con opciones que no existen
services:
  api:
    image: node:20
    auto_restart: always    # No existe — lo correcto es "restart: always"
    memory_limit: 512m      # No existe — deploy.resources.limits.memory
```

### 5. Comportamientos de API asumidos

```javascript
// El agente asume que fetch lanza excepciones en errores HTTP
try {
  const response = await fetch("/api/users");
  const users = await response.json();
} catch (error) {
  console.error("Error HTTP:", error.status);
  // fetch NO lanza excepciones para respuestas 4xx/5xx
  // Solo lanza para errores de red. Hay que verificar response.ok
}
```

---

## Cómo Detectar Alucinaciones

### Primera línea: compilar y ejecutar

El 60-70% de las alucinaciones fallan en compilación o ejecución:

```bash
# JavaScript/TypeScript
npm run build    # Detecta imports incorrectos y tipos inexistentes
npm test         # Detecta comportamientos asumidos

# Python
python -c "import modulo_sospechoso"  # Verifica que el import existe
python -m pytest                       # Ejecuta tests
```

### Segunda línea: verificar imports y dependencias

```bash
# Verificar que un paquete npm tiene el export esperado
npm info express-validator exports

# Verificar que un módulo Python tiene el atributo esperado
python -c "import pandas; print(hasattr(pandas.DataFrame, 'auto_clean'))"
```

### Tercera línea: desconfiar del código no ejecutado

Si el agente dice "esto debería funcionar" pero no lo ha ejecutado, trátalo como sospechoso:

```text
Ejecuta ese código y muéstrame el output antes de continuar.
```

---

## Cómo Prevenir Alucinaciones

### 1. Dar acceso a documentación relevante

Usar MCP servers, `@archivo` en Cursor, o pegar snippets de docs oficiales con la instrucción: "No inventes métodos — usa solo lo que aparece en esta documentación."

### 2. Incluir reglas en CLAUDE.md

```markdown
## Reglas de verificación
- Siempre ejecuta los tests después de cada cambio
- No uses funciones sin verificar que existen en la versión instalada
- Si no estás seguro de que algo existe, verifica antes de usarlo
```

### 3. Pedir ejecución + output

```text
Implementa la función y ejecútala con datos de prueba. Muéstrame el resultado real.
```

---

## Consejos por Herramienta

| Herramienta | Estrategia anti-alucinación |
|-------------|----------------------------|
| **Claude Code** | `Bash` para ejecutar tests, `Read` para verificar que archivos/funciones existen, MCP para docs |
| **Cursor** | `@docs` para referenciar documentación, terminal integrado para ejecutar |
| **Copilot** | El IDE marca imports incorrectos en tiempo real con subrayado rojo |
| **Windsurf/Cline** | Similar a Claude Code: pide ejecución explícita y revisa el output |

---

## Resumen

- Las alucinaciones son imports, métodos o comportamientos inventados que el agente presenta con total confianza
- Compilar y ejecutar es tu primera defensa — detecta la mayoría
- Verificar imports contra documentación oficial cubre el resto
- Prevenir es mejor que detectar: da acceso a docs, incluye reglas en CLAUDE.md, pide ejecución de tests
- Nunca confíes en "debería funcionar" — ejecuta y verifica siempre
