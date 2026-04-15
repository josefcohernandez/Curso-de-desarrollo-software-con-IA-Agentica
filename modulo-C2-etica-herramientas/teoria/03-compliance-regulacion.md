# Compliance y Sectores Regulados

## Introducción

Si trabajas en un sector regulado — banca, salud, gobierno, seguros — no basta con "tener cuidado". Existen regulaciones específicas que afectan cómo puedes usar IA en tu proceso de desarrollo. Este capítulo cubre las principales regulaciones y los controles que necesitas implementar.

---

## GDPR y Datos Personales

### Cuándo aplica

El GDPR (General Data Protection Regulation) aplica si tu software procesa datos de personas en la Unión Europea, independientemente de dónde esté tu empresa.

### Implicaciones para desarrollo con IA

| Aspecto | Implicación |
|---------|-------------|
| **Código fuente con schemas** | Si tus schemas de base de datos contienen campos de datos personales (nombre, email, DNI), el agente los ve |
| **Fixtures con datos reales** | Si los datos de test incluyen datos personales reales, estás enviando PII a un tercero |
| **Localización de datos** | El proveedor de IA debe cumplir con los requisitos de localización de datos del GDPR |
| **Derecho al olvido** | ¿Puede el proveedor borrar datos que recibió en un prompt? Verificar |

### Controles recomendados

1. Sanitizar todos los fixtures y datos de ejemplo antes de que el agente los lea
2. Verificar que el proveedor de IA tiene certificación GDPR o equivalente
3. Usar opciones de geo-restricción si están disponibles (ej: Anthropic ofrece `inference_geo: "eu-only"`)
4. Documentar el uso de IA en el registro de actividades de tratamiento (ROPA)

---

## SOC 2 / ISO 27001

### Cuándo aplica

Si tu empresa tiene certificación SOC 2 o ISO 27001, el uso de cualquier herramienta nueva debe evaluarse dentro del marco de gestión de riesgos.

### Implicaciones para desarrollo con IA

| Control | Acción requerida |
|---------|-----------------|
| **Inventario de proveedores** | Añadir el proveedor de IA al inventario de terceros |
| **Evaluación de riesgos** | Documentar riesgos de enviar código a un servicio externo |
| **Control de acceso** | Definir quién puede usar la herramienta y con qué datos |
| **Logging** | Registrar uso de la herramienta (no el contenido, pero sí quién, cuándo, con qué frecuencia) |
| **Revisión periódica** | Incluir la herramienta de IA en las revisiones de seguridad trimestrales |

### Template de evaluación para SOC 2

```markdown
## Evaluación de Riesgo — Herramienta de IA para Desarrollo

### Proveedor
- Nombre: [proveedor]
- Tipo de servicio: Asistente de desarrollo con IA
- Datos procesados: código fuente, configuraciones de desarrollo

### Riesgos identificados
1. Exposición de código fuente propietario a tercero
2. Posible inclusión de secrets en prompts
3. Código generado con vulnerabilidades de seguridad

### Controles implementados
1. Política de deny para archivos sensibles (.env, .pem, credentials)
2. Formación del equipo sobre higiene de prompts
3. SAST obligatorio en CI/CD para código generado
4. Code review obligatorio para todo código (IA y humano)

### Riesgo residual: [Bajo / Medio / Alto]
### Aprobado por: [Nombre, Fecha]
```

---

## Sectores Específicos

### Banca y Finanzas (PCI-DSS)

PCI-DSS regula el manejo de datos de tarjetas de crédito. Si tu código procesa, almacena o transmite datos de tarjetas:

| Requisito | Implicación |
|-----------|-------------|
| Requisito 6.5 | El código que maneja datos de tarjetas debe ser revisado manualmente, no solo por IA |
| Requisito 6.6 | Revisión de código o WAF obligatorio antes de producción |
| Requisito 3.4 | Datos de tarjetas nunca deben aparecer en logs ni en prompts al agente |
| Requisito 8 | Control de acceso: la herramienta de IA no debe tener acceso a entornos PCI |

**Regla práctica**: en módulos de tu código que manejan datos PCI, usa la IA solo para scaffolding y tests. La lógica de negocio que toca datos de tarjetas se revisa manualmente.

### Salud (HIPAA)

HIPAA (Health Insurance Portability and Accountability Act) protege datos de pacientes (PHI - Protected Health Information):

| Aspecto | Requisito |
|---------|-----------|
| **BAA** | Necesitas un Business Associate Agreement con el proveedor de IA si le envías PHI |
| **PHI en código** | Los schemas, fixtures y logs no deben contener datos de pacientes reales |
| **Audit trail** | El uso de IA en código que procesa PHI debe estar documentado |
| **Minimum necessary** | Solo enviar al agente el mínimo código necesario para la tarea |

**Regla práctica**: nunca envíes datos de pacientes reales al agente. Usa datos sintéticos para desarrollo y testing.

### Automotriz y Aviación (Safety-Critical)

Si desarrollas software safety-critical (sistemas de control de vehículos, aviación, dispositivos médicos):

- El código generado por IA **no puede** sustituir la verificación formal requerida por estándares como ISO 26262 o DO-178C
- La IA puede ayudar a generar borradores, pero el proceso de verificación y validación no cambia
- Documentar explícitamente qué partes del código tuvieron asistencia de IA

---

## Checklist de Compliance por Sector

```markdown
## Compliance — Uso de IA en Desarrollo

### General (todos los sectores)
- [ ] Proveedor de IA añadido al inventario de terceros
- [ ] Política de uso de IA documentada y comunicada
- [ ] Archivos sensibles excluidos del acceso del agente
- [ ] Code review obligatorio para todo código

### GDPR
- [ ] Datos personales sanitizados en fixtures y ejemplos
- [ ] Proveedor cumple con localización de datos
- [ ] Uso documentado en el ROPA

### SOC 2 / ISO 27001
- [ ] Evaluación de riesgos completada y aprobada
- [ ] Logging de uso implementado
- [ ] Incluido en revisiones de seguridad periódicas

### PCI-DSS
- [ ] Agente sin acceso a entornos PCI
- [ ] Revisión manual obligatoria para código que maneja tarjetas
- [ ] Datos de tarjetas nunca en prompts ni en logs

### HIPAA
- [ ] BAA firmado con el proveedor (o uso sin PHI)
- [ ] Datos sintéticos para desarrollo y test
- [ ] Audit trail del uso de IA en código PHI
```

---

## EU AI Act (Reglamento Europeo de Inteligencia Artificial)

### Contexto y cronología

El EU AI Act es la primera regulación integral de IA a nivel mundial. Su implementación es gradual:

| Fecha | Hito |
|-------|------|
| **Febrero 2025** | Entrada en aplicación parcial: prohibiciones de prácticas de IA inaceptables |
| **Agosto 2025** | Obligaciones para modelos de IA de propósito general (GPAI) |
| **2 de agosto de 2026** | **Aplicación completa** de las reglas para sistemas de IA de alto riesgo |

Fuente: [artificialintelligenceact.eu/implementation-timeline/](https://artificialintelligenceact.eu/implementation-timeline/)

### Relevancia para desarrollo con IA agéntica

Las herramientas de generación de código con IA (como Claude Code, Copilot, Cursor) **no están clasificadas como "alto riesgo"** bajo el AI Act actual. Sin embargo, hay escenarios donde sí afecta:

| Escenario | Impacto |
|-----------|---------|
| **Desarrollo de software para dispositivos médicos** | Si usas IA para generar código de un dispositivo médico (alto riesgo), la organización puede necesitar cumplir con los requisitos del AI Act |
| **Infraestructura crítica** | Código generado por IA para sistemas de control de energía, agua o transporte cae bajo escrutinio regulatorio |
| **Desarrollo general** | Impacto mínimo — los desarrolladores individuales no se ven afectados directamente |

### Requisitos clave

1. **Transparencia**: los usuarios deben ser informados cuando interactúan con un sistema de IA. En el contexto de desarrollo, esto refuerza la práctica de documentar qué código fue generado o asistido por IA.

2. **Sandboxes regulatorios**: los estados miembros de la UE deben establecer sandboxes regulatorios para IA, lo que puede crear oportunidades para experimentar con herramientas de IA en entornos controlados.

3. **Documentación técnica**: para sistemas de alto riesgo, se requiere documentación detallada del ciclo de vida del sistema, incluyendo las herramientas usadas en su desarrollo.

### Implicación práctica

Para la mayoría de los equipos de desarrollo, el EU AI Act **no cambia el día a día**. La excepción son equipos enterprise en sectores regulados (salud, infraestructura crítica, seguridad) que deben:

- Evaluar si el software que desarrollan cae bajo la clasificación de "alto riesgo"
- Documentar el uso de herramientas de IA en el proceso de desarrollo
- Mantener trazabilidad del código generado por IA vs. escrito manualmente
- Consultar con el equipo legal para determinar obligaciones específicas

---

## Navegación

| | |
|---|---|
| **Anterior** | [02-privacidad-datos.md](02-privacidad-datos.md) |
| **Siguiente** | [04-responsible-disclosure.md](04-responsible-disclosure.md) |
| **Módulo** | [README del módulo](../README.md) |
