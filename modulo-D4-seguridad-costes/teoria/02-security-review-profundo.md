# Security Code Review Profundo

## Introducción

Un security review superficial aplica un checklist genérico (OWASP Top 10) y marca casillas. Un review **profundo** entiende las vulnerabilidades específicas de tu stack, busca patrones de ataque concretos y produce hallazgos accionables con línea de código, exploit potencial y mitigación. Con un agente de IA, puedes hacer reviews profundos en una fracción del tiempo -- si sabes qué pedir.

---

## Más Allá del Checklist de OWASP

### El problema de los checklists genéricos

OWASP Top 10 es un punto de partida, no un destino. Sus categorías son amplias ("Broken Access Control", "Injection") y no te dicen qué buscar específicamente en tu stack. Un review que solo marca "SQL Injection: No encontrado" puede pasar por alto un prototype pollution en Node.js que no aparece en la lista.

### El enfoque profundo

En vez de preguntar "¿hay SQL injection?", pregunta: "¿hay algún lugar donde el input del usuario llega a una query sin parametrizar, incluyendo queries dinámicas construidas con template literals, ORMs mal configurados o raw queries en migrations?"

---

## Vulnerabilidades Específicas por Stack

| Stack | Vulnerabilidades específicas | Qué pedir al agente |
|-------|------------------------------|---------------------|
| **Node/Express** | Prototype pollution, SSRF, ReDoS, event loop blocking | "Busca prototype pollution en objetos de request y ReDoS en regex de validación" |
| **Python/Django** | Template injection, pickle deserialization, mass assignment | "Verifica que no hay user input en template rendering ni deserialización insegura" |
| **React** | XSS via `dangerouslySetInnerHTML`, open redirect, exposed API keys | "Busca uso de dangerouslySetInnerHTML y verifica sanitización" |
| **SQL** | Injection, privilege escalation, excessive data exposure | "Verifica que TODOS los queries usan parametrización, sin excepciones" |
| **Java/Spring** | JNDI injection, insecure deserialization, actuator exposure | "Busca endpoints de actuator expuestos y deserialización de objetos no confiables" |
| **Go** | Integer overflow, race conditions, unsafe pointer usage | "Busca race conditions en goroutines que acceden a datos compartidos sin mutex" |

### Ejemplo: Prototype Pollution en Node.js

```javascript
// VULNERABLE: merge recursivo sin protección
function deepMerge(target, source) {
  for (const key in source) {
    if (typeof source[key] === "object") {
      target[key] = deepMerge(target[key] || {}, source[key]);
    } else {
      target[key] = source[key];
    }
  }
  return target;
}

// Un atacante envía: {"__proto__": {"isAdmin": true}}
// Ahora TODOS los objetos tienen isAdmin = true
app.put("/api/settings", (req, res) => {
  deepMerge(userSettings, req.body);  // Peligroso
});
```

```javascript
// SEGURO: filtrar keys peligrosas
function safeMerge(target, source) {
  const BLOCKED_KEYS = ["__proto__", "constructor", "prototype"];
  for (const key in source) {
    if (BLOCKED_KEYS.includes(key)) continue;
    if (typeof source[key] === "object" && source[key] !== null) {
      target[key] = safeMerge(target[key] || {}, source[key]);
    } else {
      target[key] = source[key];
    }
  }
  return target;
}
```

---

## Prompt Template para Security Review Profundo

Este template produce resultados significativamente mejores que un prompt genérico:

```text
Realiza un security review del módulo [path].
No uses un checklist genérico. Busca específicamente:

1. Inputs no validados que llegan a queries, templates o comandos del sistema
2. Secrets en código fuente, logs, respuestas HTTP o variables de entorno 
   expuestas al frontend
3. Race conditions en operaciones que modifican datos (especialmente 
   transacciones financieras o cambios de estado)
4. Authorization bypass: ¿puede el usuario A acceder a datos del usuario B?
   Busca IDOR (Insecure Direct Object Reference) en todos los endpoints
5. Dependencias con CVEs conocidos que afecten a funciones que usamos

Para cada hallazgo proporciona:
- Severidad: Crítica / Alta / Media / Baja
- Archivo y línea exacta
- Descripción del exploit potencial (cómo un atacante lo aprovecharía)
- Mitigación con código de ejemplo

Si no encuentras nada en una categoría, explica qué verificaste y por qué 
consideras que está seguro.
```

### Por qué funciona este template

1. **Específico**: lista categorías concretas en vez de "busca vulnerabilidades"
2. **Exige evidencia**: "archivo y línea exacta" evita hallazgos vagos
3. **Pide el exploit**: obliga al agente a pensar como atacante
4. **Incluye negativos**: "explica qué verificaste" evita falsos negativos silenciosos

---

## Patrones de Security Review por Capa

### Capa de API

```text
Revisa todos los endpoints de la API buscando:
- Endpoints sin autenticación que deberían tenerla
- Endpoints que no verifican autorización (cualquier usuario autenticado 
  puede acceder a cualquier recurso)
- Rate limiting ausente en endpoints sensibles (login, registro, 
  recuperación de contraseña)
- Respuestas que incluyen más datos de los necesarios (over-fetching)
Lista cada endpoint con su estado de seguridad actual.
```

### Capa de datos

```text
Revisa todas las interacciones con la base de datos buscando:
- Queries construidas con concatenación de strings en vez de 
  parametrización
- Campos sensibles (contraseñas, tokens, tarjetas) sin cifrar en reposo
- Migrations que otorgan permisos excesivos al usuario de la aplicación
- Backups o dumps de base de datos accesibles desde la red
```

### Capa de autenticación

```text
Revisa el sistema de autenticación buscando:
- Tokens JWT sin expiración o con expiración excesiva (> 24h)
- Refresh tokens almacenados en localStorage (vulnerables a XSS)
- Endpoint de login sin protección contra brute force
- Recuperación de contraseña que revela si un email existe en el sistema
- Sesiones que no se invalidan al cambiar la contraseña
```

---

## Workflow de Security Review con IA

1. **Scope**: define qué módulos revisar (no intentes revisar todo a la vez)
2. **Review por stack**: usa los prompts específicos de tu tecnología
3. **Review por capa**: API, datos, autenticación, frontend
4. **Triaje**: clasifica hallazgos por severidad
5. **Fix**: pide al agente que implemente las mitigaciones
6. **Verify**: pide al agente que verifique que los fixes son correctos
7. **Document**: registra hallazgos y mitigaciones para el equipo

### Frecuencia recomendada

| Trigger | Tipo de review |
|---------|---------------|
| Cada PR con cambios en auth/permisos | Review focalizado de autenticación |
| Cada sprint | Review de endpoints nuevos o modificados |
| Cada trimestre | Review profundo completo del sistema |
| Actualización mayor de dependencias | Review de dependencias + regresión |

---

## Resumen

- Un security review profundo va más allá de OWASP: busca vulnerabilidades específicas de tu stack
- El prompt template con 5 categorías específicas produce hallazgos accionables, no genéricos
- Revisa por capas (API, datos, auth, frontend) para no dejar áreas sin cubrir
- El agente de IA encuentra patrones de vulnerabilidad rápidamente, pero tú evalúas la severidad real y apruebas las mitigaciones
- La revisión de seguridad no es un evento único: es un proceso continuo con triggers definidos
