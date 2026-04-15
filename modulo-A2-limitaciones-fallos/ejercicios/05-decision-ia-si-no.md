# Ejercicio 5: Decisión "IA Sí / IA No"

## Objetivo

Clasificar 10 tareas reales de desarrollo como "delegar a IA" o "hacer manualmente", justificando cada decisión con los criterios aprendidos en la teoría 06.

**Tiempo estimado: 10 minutos**

---

## Las 10 Tareas

Para cada tarea, decide: **¿la delegarías a un agente de código o la harías manualmente?**

Copia la tabla y rellena las columnas "Decisión" y "Justificación":

| # | Tarea | Decisión (IA / Manual) | Justificación |
|---|-------|------------------------|---------------|
| 1 | Añadir validación de inputs a un formulario de registro | | |
| 2 | Diseñar el esquema de base de datos para una feature nueva completa | | |
| 3 | Corregir un bug de alineación CSS en un componente | | |
| 4 | Implementar rotación de tokens JWT con refresh tokens | | |
| 5 | Escribir tests unitarios para un módulo existente | | |
| 6 | Decidir entre arquitectura monolítica y microservicios | | |
| 7 | Migrar un proyecto de CommonJS a ESM | | |
| 8 | Revisar la seguridad del flujo de autenticación | | |
| 9 | Generar documentación de API a partir del código fuente | | |
| 10 | Optimizar una query SQL que causa lentitud en producción | | |

---

## Criterios de Decisión

Usa estos criterios de la teoría 06 para guiar tu análisis:

- **Reversibilidad**: ¿se puede deshacer fácilmente si sale mal?
- **Verificabilidad**: ¿puedes comprobar objetivamente si el resultado es correcto?
- **Riesgo**: ¿cuál es el coste de un error?
- **Contexto necesario**: ¿la tarea requiere entender el negocio, la organización o restricciones no técnicas?
- **Mecánica vs. juicio**: ¿es trabajo repetitivo/mecánico o requiere decisiones de diseño?

---

## Análisis Esperado

Después de rellenar la tabla, compara con este análisis orientativo:

### Tarea 1: Validación de inputs en formulario
- **Recomendación**: IA
- Tarea mecánica y verificable. Los tests confirman que funciona. Fácil de revertir.

### Tarea 2: Diseño de esquema de base de datos
- **Recomendación**: Manual (IA como borrador)
- Requiere entender relaciones de negocio, patrones de acceso y crecimiento futuro. Usa IA para generar un borrador, pero revisa y decide tú.

### Tarea 3: Bug de alineación CSS
- **Recomendación**: IA
- Verificable visualmente. Bajo riesgo. El peor caso es que no quede bien y lo ajustas.

### Tarea 4: Rotación de tokens JWT
- **Recomendación**: IA con supervisión estrecha
- Es código de seguridad. El agente puede generar la estructura, pero debes revisar cada detalle: tiempos de expiración, almacenamiento seguro, invalidación.

### Tarea 5: Tests unitarios para módulo existente
- **Recomendación**: IA
- Excelente caso de uso para IA. Los tests se auto-verifican (pasan o no pasan). El agente puede generar cobertura rápidamente.

### Tarea 6: Monolito vs. microservicios
- **Recomendación**: Manual
- Decisión arquitectónica que depende del tamaño del equipo, presupuesto, infraestructura, timeline y docenas de factores que el agente no conoce.

### Tarea 7: Migración CommonJS a ESM
- **Recomendación**: IA
- Trabajo mecánico y repetitivo. Cambiar `require` por `import`, ajustar `package.json`. Verificable con la test suite.

### Tarea 8: Revisar seguridad del flujo de autenticación
- **Recomendación**: Manual
- Revisión de seguridad requiere entender vectores de ataque, contexto de despliegue y threat model. Usa IA para generar un checklist, pero la revisión es tuya.

### Tarea 9: Generar documentación de API
- **Recomendación**: IA
- Trabajo mecánico que el agente hace muy bien. Verificable comparando con el código fuente.

### Tarea 10: Optimizar query SQL lenta
- **Recomendación**: Manual con apoyo de IA
- Necesitas profiling real (EXPLAIN ANALYZE) para entender el problema. El agente no puede ejecutar queries en tu base de producción ni medir tiempos reales. Usa IA para sugerir índices o reescrituras después de entender el plan de ejecución.

---

## Reflexión

Después de completar el ejercicio, responde:

1. **¿Cuántas tareas clasificaste diferente al análisis orientativo?** Si fue más de 3, revisa los criterios de decisión.
2. **¿Qué patrón ves?** Las tareas mecánicas y verificables se delegan; las que requieren juicio, contexto de negocio o tienen alto riesgo se hacen manualmente.
3. **¿Cuál es tu regla personal?** Formula una frase que resuma tu criterio de delegación.

---

## La Regla de Oro

> Si puedes verificar el resultado con tests o inspección visual, delega a IA.
> Si la verificación requiere juicio experto o contexto de negocio, hazlo tú (o usa IA como borrador inicial).
