# Threat Modeling Asistido por IA

## Introducción

Threat modeling es el proceso de identificar amenazas de seguridad de forma **sistemática** antes de que sean explotadas. No se trata de "pensar en qué podría salir mal" de forma improvisada, sino de aplicar un framework estructurado que garantice que no se te escapa nada. Un agente de IA es un compañero ideal para este proceso: puede analizar arquitecturas complejas, cruzar datos de CVE databases y generar matrices de amenazas en minutos.

---

## STRIDE con Asistencia de IA

### Qué es STRIDE

STRIDE es un framework de threat modeling desarrollado por Microsoft que clasifica las amenazas en 6 categorías:

| Categoría | Amenaza | Ejemplo |
|-----------|---------|---------|
| **S**poofing | Suplantación de identidad | Un atacante se hace pasar por otro usuario |
| **T**ampering | Manipulación de datos | Modificar un pedido después de ser procesado |
| **R**epudiation | Negación de acciones | Un usuario niega haber realizado una compra |
| **I**nformation Disclosure | Fuga de información | Exponer datos sensibles en logs o respuestas HTTP |
| **D**enial of Service | Denegación de servicio | Saturar un endpoint con requests hasta que caiga |
| **E**levation of Privilege | Escalada de privilegios | Un usuario normal accede a funciones de admin |

### Prompt para STRIDE con IA

```text
Analiza la siguiente arquitectura usando el framework STRIDE.
Para cada categoría, identifica al menos 2 amenazas potenciales.

Arquitectura:
- API REST en Node/Express con autenticación JWT
- Base de datos PostgreSQL con datos de usuarios y pedidos
- Frontend React que consume la API
- Almacenamiento de archivos en S3
- Webhook que recibe notificaciones de pagos desde Stripe

Para cada amenaza proporciona:
1. Descripción específica (no genérica)
2. Componente afectado
3. Impacto (alto/medio/bajo)
4. Probabilidad (alta/media/baja)
5. Mitigación recomendada con ejemplo de código o configuración
```

### Ejemplo de resultado esperado

```markdown
## S - Spoofing

### Amenaza S1: Suplantación de JWT sin verificación de audiencia
- **Componente**: API middleware de autenticación
- **Descripción**: Si el JWT no verifica el claim `aud` (audience), un token 
  emitido para otro servicio podría usarse en esta API
- **Impacto**: Alto - acceso completo como cualquier usuario
- **Probabilidad**: Media - requiere acceso a un token válido de otro servicio
- **Mitigación**: Verificar `aud` en cada validación de token

### Amenaza S2: Falsificación de webhook de Stripe
- **Componente**: Endpoint /webhooks/stripe
- **Descripción**: Sin verificación de firma, un atacante puede enviar webhooks 
  falsos para marcar pedidos como pagados
- **Impacto**: Alto - fraude directo
- **Probabilidad**: Alta - el endpoint es público
- **Mitigación**: Verificar firma HMAC de Stripe en cada webhook
```

---

## Análisis de Dependencias

### Por qué importa

Tu código puede ser impecable, pero si una dependencia tiene una vulnerabilidad conocida, tu aplicación es vulnerable. El agente puede auditar tus dependencias de forma exhaustiva.

### Prompt para auditoría de dependencias

```text
Revisa las dependencias del proyecto analizando package.json (o 
requirements.txt). Para cada dependencia, verifica:

1. ¿Tiene CVEs conocidos? Indica el CVE ID y la severidad
2. ¿Está en mantenimiento activo? (último commit > 1 año = riesgo)
3. ¿Tiene licencia compatible con uso comercial?
4. ¿Hay una versión más reciente con fixes de seguridad?

Prioriza los hallazgos por severidad (crítico > alto > medio > bajo).
Para cada CVE encontrado, indica si nos afecta o si la función 
vulnerable no la usamos.
```

### Herramientas complementarias

| Herramienta | Comando | Qué analiza |
|-------------|---------|-------------|
| `npm audit` | `npm audit --json` | CVEs en dependencias npm |
| `pip-audit` | `pip-audit --format=json` | CVEs en dependencias Python |
| `trivy` | `trivy fs --scanners vuln .` | Vulnerabilidades en filesystem |
| `snyk` | `snyk test` | CVEs + análisis de código |

El agente puede ejecutar estas herramientas y luego analizar los resultados en contexto:

```text
Ejecuta npm audit y analiza los resultados. Para cada vulnerabilidad 
encontrada:
1. ¿Es explotable en nuestro caso de uso?
2. ¿Podemos actualizar sin romper nada?
3. Si no podemos actualizar, ¿hay un workaround?
```

---

## Attack Surface Mapping

### Qué es

El attack surface (superficie de ataque) es el conjunto de todos los puntos donde un atacante puede interactuar con tu sistema. Mapearla es el primer paso para protegerla.

### Puntos de entrada típicos

| Punto de entrada | Riesgo | Qué verificar |
|-----------------|--------|---------------|
| Endpoints REST/GraphQL | Alto | Autenticación, autorización, validación de input |
| Uploads de archivos | Alto | Tipo de archivo, tamaño, escaneo de malware |
| Webhooks entrantes | Alto | Verificación de firma, rate limiting |
| Formularios web | Medio | XSS, CSRF, validación client + server |
| WebSockets | Medio | Autenticación en conexión, rate limiting |
| Variables de entorno | Medio | No expuestas en logs, frontend o respuestas |
| Jobs programados (cron) | Bajo | Permisos mínimos, logging de ejecución |

### Prompt para attack surface mapping

```text
Analiza el proyecto y genera un mapa completo de la superficie de ataque.
Para cada punto de entrada encontrado:
1. Ruta o componente
2. Tipo (endpoint, upload, webhook, formulario, etc.)
3. ¿Requiere autenticación? ¿Y autorización?
4. ¿Qué datos recibe del exterior?
5. ¿Esos datos se validan antes de procesarse?
6. Nivel de riesgo (alto/medio/bajo)

Presenta el resultado como tabla ordenada por riesgo descendente.
```

---

## Workflow Completo de Threat Modeling

1. **Documenta la arquitectura**: diagrama de componentes, flujos de datos, puntos de entrada
2. **STRIDE**: aplica el framework a cada componente
3. **Dependencias**: audita CVEs y estado de mantenimiento
4. **Attack surface**: mapa completo de puntos de entrada
5. **Priorización**: ordena amenazas por impacto x probabilidad
6. **Mitigación**: implementa las mitigaciones empezando por las de mayor prioridad
7. **Verificación**: pide al agente que verifique que las mitigaciones son efectivas

---

## Resumen

- Threat modeling no es opcional cuando se desarrolla con IA: el agente genera código rápido, pero no necesariamente seguro
- STRIDE es un framework probado que garantiza cobertura sistemática de amenazas
- El análisis de dependencias es la forma más rápida de encontrar vulnerabilidades: muchos exploits usan dependencias conocidas
- El attack surface mapping te da visibilidad completa de dónde puede atacar un adversario
- El agente de IA acelera cada paso del proceso, pero tú decides la priorización y apruebas las mitigaciones
