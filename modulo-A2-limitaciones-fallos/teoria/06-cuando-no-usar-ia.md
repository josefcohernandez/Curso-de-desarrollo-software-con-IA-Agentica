# Cuándo NO Usar IA

## La Premisa

Los agentes de código son herramientas extraordinariamente productivas para la mayoría de tareas de desarrollo. Pero hay escenarios donde un humano es significativamente mejor, o donde el riesgo de error de la IA es **inaceptable** dado el coste de las consecuencias.

Saber cuándo NO usar IA es tan valioso como saber usarla bien. Un profesional no es quien delega todo, sino quien delega lo correcto.

---

## 6 Tareas Donde el Humano es Mejor

| Tarea | Por qué NO usar IA | Qué hacer en su lugar |
|-------|--------------------|-----------------------|
| **Decisiones de arquitectura ambiguas** | El agente elige una opción sin entender el contexto de negocio, los trade-offs a largo plazo ni las restricciones organizativas | Diseñar tú la arquitectura. Usar IA para implementar la decisión ya tomada |
| **Código criptográfico o de seguridad crítica** | Errores sutiles son catastróficos y difíciles de detectar. Un parámetro incorrecto en una función de hash puede hacer que todo "funcione" pero sea inseguro | Usar librerías probadas y auditadas. Revisar manualmente. Consultar expertos en seguridad |
| **Optimización de performance extrema** | El agente no puede medir performance real, no entiende el hardware subyacente ni los patrones de acceso a memoria | Hacer profiling manual primero. Usar IA para generar benchmarks o código de prueba |
| **Código con implicaciones legales o regulatorias** | El agente no entiende GDPR, HIPAA, PCI-DSS ni el marco legal de tu jurisdicción. Un error puede tener consecuencias legales | Revisar manualmente todo código que maneja datos personales o financieros. Usar IA como borrador inicial que un experto revisa |
| **Debugging de condiciones de carrera** | Requiere razonamiento temporal sobre concurrencia que los LLM manejan mal. Los race conditions dependen del timing, no de la lógica estática | Usar herramientas dedicadas: thread sanitizers, debuggers con breakpoints condicionales, logging de bajo nivel |
| **Eliminación de datos en producción** | Operación irreversible donde el coste del error es infinito. No hay "undo" en `DELETE FROM users` | Siempre manual con doble verificación humana. Backup previo obligatorio. Revisión por pares del script |

---

## Análisis Detallado

### Decisiones de arquitectura

```text
Prompt peligroso:
"¿Debería usar microservicios o monolito para mi nueva aplicación?"

El agente te dará una respuesta razonada... basada en patrones generales.
No sabe que tu equipo tiene 3 personas, que el deadline es en 2 meses,
que ya tenéis infraestructura Kubernetes, o que el CTO prefiere simplicidad.
```

**Lo correcto**: decide tú la arquitectura. Luego pide al agente que la implemente.

### Código criptográfico

```javascript
// El agente puede generar esto, que "funciona" pero es INSEGURO:
const crypto = require("crypto");
const hash = crypto.createHash("md5").update(password).digest("hex");
// MD5 no es seguro para passwords — debería ser bcrypt o argon2

// Peor aún, puede inventar parámetros:
const key = crypto.pbkdf2Sync(password, salt, 1000, 32, "sha256");
// 1000 iteraciones es insuficiente — mínimo recomendado: 600,000
```

Un humano con conocimiento de seguridad detecta estos errores. El agente no sabe que son errores.

### Performance extrema

El agente puede sugerir optimizaciones basadas en "mejores prácticas generales", pero no puede:
- Ejecutar un profiler y analizar los resultados
- Entender los patrones de acceso a caché de tu hardware
- Medir el impacto real de un cambio en producción
- Distinguir entre un bottleneck de CPU, I/O o memoria

### Implicaciones legales

```javascript
// El agente puede generar código que técnicamente funciona
// pero viola regulaciones:
app.post("/api/register", (req, res) => {
  const user = createUser(req.body);
  analytics.track(user.email, user.ip, user.location);
  // ¿Hay consentimiento? ¿Se informa al usuario? ¿GDPR?
});
```

### Condiciones de carrera

```javascript
// El agente puede "arreglar" un race condition con un fix que no funciona:
let isProcessing = false;

async function processOrder(order) {
  if (isProcessing) return;  // El agente cree que esto previene la race condition
  isProcessing = true;       // Pero entre el check y el set, otro request puede entrar
  await doExpensiveWork(order);
  isProcessing = false;
}
// Necesitas un mutex real, no una variable booleana
```

### Eliminación de datos

```sql
-- NUNCA dejes que un agente ejecute esto sin doble verificación humana:
DELETE FROM orders WHERE created_at < '2024-01-01';
-- ¿Estás seguro de la fecha? ¿Hay foreign keys? ¿Hay backup?
```

---

## La Regla General

> **Usa IA para generar, nunca para decidir sin supervisión en contextos de alto riesgo.**

Esto significa:
- **Generar**: borrador de código, implementación de diseños ya decididos, tests, documentación, refactoring mecánico
- **Decidir**: arquitectura, seguridad, compliance, operaciones destructivas en producción

La IA es tu **asistente**, no tu **jefe**. Tú decides qué hacer; la IA te ayuda a hacerlo más rápido.

---

## Resumen

- Hay 6 categorías de tareas donde la IA no debería operar sin supervisión estrecha
- El factor común es: alto riesgo + consecuencias difíciles de detectar o revertir
- Decisiones de arquitectura, seguridad, compliance, performance y datos en producción requieren criterio humano
- La regla: IA para generar, humano para decidir en contextos de alto riesgo
- Ser profesional con IA incluye saber cuándo apagar el agente y pensar por ti mismo
