# Ejercicio: Auditoría de Privacidad

## Objetivo

Revisar la configuración actual de tu herramienta de IA para desarrollo y verificar qué datos se envían, qué opciones de privacidad tienes disponibles, y si tu configuración es adecuada para tu contexto.

**Tiempo estimado: 15 minutos**

---

## Instrucciones

### Parte 1: Inventario de Datos

Revisa tu herramienta de IA actual y documenta:

| Dato | ¿Se envía? | ¿Cómo verificarlo? |
|------|-----------|---------------------|
| Código fuente del proyecto | | |
| Archivos `.env` o de configuración | | |
| Logs y stack traces | | |
| Variables de entorno del sistema | | |
| Historial de conversaciones | | |
| Metadatos del proyecto (estructura, dependencias) | | |

### Parte 2: Configuración de Privacidad

Busca y documenta las opciones de privacidad disponibles en tu herramienta:

```markdown
## Auditoría de Privacidad — [Nombre de la herramienta]

### Política de retención de datos
- ¿El proveedor retiene los datos de tus prompts? [ ] Sí  [ ] No  [ ] Configurable
- ¿Durante cuánto tiempo? ___
- ¿Se usan tus datos para entrenar modelos? [ ] Sí  [ ] No  [ ] Opt-out disponible

### Exclusiones configuradas
- ¿Tienes archivos excluidos del acceso del agente? [ ] Sí  [ ] No
- Lista de exclusiones actuales: ___

### Opciones de geo-restricción
- ¿Puedes elegir la región de procesamiento? [ ] Sí  [ ] No
- Región actual: ___

### Opciones de deployment
- ¿Hay opción de cloud privado? [ ] Sí  [ ] No
- ¿Hay opción on-premise? [ ] Sí  [ ] No
```

### Parte 3: Evaluación de Riesgo

Usando la evaluación rápida de [02-privacidad-datos.md](../teoria/02-privacidad-datos.md), determina tu nivel de riesgo:

```markdown
1. ¿Tu código fuente es confidencial?                    [ ] Sí  [ ] No
2. ¿Tu proyecto maneja datos personales (PII)?            [ ] Sí  [ ] No
3. ¿Estás en un sector regulado?                          [ ] Sí  [ ] No
4. ¿Tus fixtures de test contienen datos reales?          [ ] Sí  [ ] No
5. ¿Tienes secrets en archivos del proyecto?              [ ] Sí  [ ] No
6. ¿Tu empresa tiene política de seguridad de datos?      [ ] Sí  [ ] No

Nivel de riesgo: [ ] Bajo  [ ] Medio  [ ] Alto
```

### Parte 4: Plan de Acción

Basándote en el nivel de riesgo, define las acciones necesarias:

| Acción | Prioridad | Estado |
|--------|-----------|--------|
| Configurar exclusiones para archivos sensibles | | |
| Sanitizar fixtures de test | | |
| Verificar política de retención del proveedor | | |
| Consultar con equipo de seguridad / legal | | |

---

## Criterios de Éxito

```markdown
- [ ] El inventario de datos está completo
- [ ] Las opciones de privacidad de la herramienta están documentadas
- [ ] El nivel de riesgo está evaluado con las 6 preguntas
- [ ] Hay un plan de acción con al menos 2 acciones concretas
- [ ] Si el riesgo es medio-alto, se ha identificado quién consultar (seguridad / legal)
```

---

## Navegación

| | |
|---|---|
| **Siguiente ejercicio** | [02-analisis-compliance.md](02-analisis-compliance.md) |
| **Módulo** | [README del módulo](../README.md) |
