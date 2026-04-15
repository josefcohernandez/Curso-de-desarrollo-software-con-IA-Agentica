# Ejercicio: Caso Ético — Código GPL Generado por IA

## Objetivo

Analizar un escenario ético real donde el agente de IA genera código que es una copia casi literal de un proyecto open source con licencia GPL, y decidir qué hacer.

**Tiempo estimado: 10 minutos**

---

## Escenario

Estás desarrollando **DataSync**, una herramienta comercial de sincronización de bases de datos (licencia propietaria, código cerrado). Le pides al agente:

```text
Implement a diff algorithm for database records that efficiently detects 
changes between two snapshots. Handle inserts, updates, and deletes.
Output a changeset that can be applied atomically.
```

El agente genera un módulo de ~150 líneas que funciona perfectamente. Los tests pasan, el rendimiento es bueno, y el código es limpio.

Dos semanas después, un compañero revisa el código y nota que tiene una **similitud del 85% con el módulo `diff_engine.py` del proyecto open source `dbsync`**, que está publicado bajo licencia **GPL v3**.

### Lo que sabes

- GPL v3 requiere que cualquier software que incorpore código GPL también sea GPL ("copyleft")
- Tu producto es comercial y propietario — no puedes publicar el código fuente
- El código ya está en producción desde hace 2 semanas
- El agente no mencionó que estuviera reproduciendo código de un proyecto existente
- El proyecto `dbsync` tiene 500 estrellas en GitHub y un mantenedor activo

---

## Instrucciones

### Parte 1: Análisis Legal

Responde:

1. ¿Viola esto la licencia GPL v3? ¿Por qué?
2. ¿Quién es responsable: tú, el agente, el proveedor de la herramienta?
3. ¿Qué riesgos legales concretos existen?

### Parte 2: Opciones de Acción

Para cada opción, evalúa pros y contras:

| Opción | Pros | Contras |
|--------|------|---------|
| A. No hacer nada (nadie se ha quejado) | | |
| B. Reescribir el módulo desde cero sin IA | | |
| C. Contactar al autor de `dbsync` y negociar una licencia comercial | | |
| D. Hacer tu producto GPL (open source) | | |
| E. Reescribir usando la misma lógica pero con implementación diferente | | |

### Parte 3: Decisión

1. ¿Qué opción elegirías? Justifica.
2. ¿Qué harías para prevenir que esto vuelva a ocurrir?
3. ¿Deberías informar a tu manager / legal? ¿Cuándo?

### Parte 4: Prevención

Diseña un proceso de 3 pasos para detectar este tipo de situaciones antes de que lleguen a producción:

```markdown
## Proceso de Detección de Código Reproducido

### Paso 1: ___
- Cuándo: ___
- Cómo: ___
- Quién: ___

### Paso 2: ___
- Cuándo: ___
- Cómo: ___
- Quién: ___

### Paso 3: ___
- Cuándo: ___
- Cómo: ___
- Quién: ___
```

---

## Criterios de Éxito

```markdown
- [ ] El análisis legal identifica correctamente la violación de GPL
- [ ] Se reconoce que la responsabilidad es del developer/empresa, no del agente
- [ ] Se evalúan al menos 3 opciones con pros y contras reales
- [ ] La decisión elegida es viable y se justifica
- [ ] El plan de prevención incluye medidas técnicas concretas (no solo "tener cuidado")
- [ ] Se contempla informar a legal/management
```

---

## Reflexión

- ¿Es justo que el developer sea responsable de código que no escribió conscientemente?
- ¿Deberían las herramientas de IA detectar y advertir cuando reproducen código con licencia restrictiva?
- ¿Cómo cambiaría tu análisis si la licencia fuera MIT en lugar de GPL?

---

## Navegación

| | |
|---|---|
| **Ejercicio anterior** | [03-comparativa-practica.md](03-comparativa-practica.md) |
| **Módulo** | [README del módulo](../README.md) |
