# Privacidad y Protección de Datos

## Introducción

Cuando usas un agente de IA para desarrollo, estás enviando código, configuraciones y contexto de tu proyecto a un servicio externo. Según tu contexto (open source, startup, enterprise regulada), esto puede ser perfectamente aceptable o un riesgo serio. Este capítulo te ayuda a evaluar tu situación y aplicar las mitigaciones adecuadas.

---

## Qué Datos Envías al Agente

Cuando interactúas con un agente de código, envías potencialmente:

| Tipo de dato | Cómo se envía | Riesgo |
|-------------|---------------|--------|
| **Código fuente** | El agente lee archivos de tu proyecto para entender contexto | El proveedor recibe tu código |
| **Logs y errores** | Al pegar stack traces o logs para debugging | Pueden contener datos de usuarios reales |
| **Configuraciones** | Archivos `.env`, `config/`, `docker-compose.yml` | Pueden contener secrets (API keys, passwords) |
| **Variables de entorno** | Si el agente ejecuta comandos que las leen | Exposición de credentials |
| **Información de negocio** | En tus prompts ("estamos migrando porque el cliente X necesita...") | Contexto de negocio sensible |
| **Datos de usuarios** | Si los fixtures de test contienen datos reales | Violación de privacidad |

---

## Riesgos por Contexto

| Contexto | Riesgo | Mitigación |
|----------|--------|------------|
| **Proyecto open source** | Bajo — el código ya es público | Cuidado solo con secrets (API keys, tokens, passwords) |
| **Startup con IP sensible** | Medio — el código es el producto | Usar API con data retention off o proveedores con cloud privado (Bedrock, Vertex) |
| **Enterprise regulada (banca, salud)** | Alto — regulaciones estrictas (GDPR, HIPAA, PCI-DSS) | Despliegue on-premise o en cloud privado. Verificar compliance |
| **Gobierno / defensa** | Muy alto — información clasificada | Solo herramientas aprobadas con certificaciones específicas (FedRAMP, etc.) |

---

## Mejores Prácticas de Privacidad

### 1. Nunca pegar secrets en prompts

Esto parece obvio, pero ocurre constantemente:

```bash
# MAL: pegar un .env con secrets reales
DATABASE_URL=postgresql://admin:s3cur3p4ss@prod-db.example.com:5432/myapp
JWT_SECRET=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

# BIEN: usar placeholders
DATABASE_URL=postgresql://<user>:<password>@<host>:5432/<database>
JWT_SECRET=<tu-jwt-secret-aqui>
```

### 2. Configurar exclusiones en el agente

Asegúrate de que el agente no pueda leer archivos sensibles:

```json
{
  "permissions": {
    "deny": [
      "Read(.env*)",
      "Read(**/secrets/**)",
      "Read(**/credentials/**)",
      "Read(**/*.pem)",
      "Read(**/*.key)"
    ]
  }
}
```

### 3. Verificar la política de retención

| Proveedor | Retención de datos (por defecto) | Opción sin retención |
|-----------|----------------------------------|---------------------|
| **Anthropic (API)** | 7 días para detección de abuso (reducido desde 30 días en sept. 2025) | Sí, configurable |
| **AWS Bedrock** | No retiene datos de inferencia | N/A (ya no retiene) |
| **GCP Vertex AI** | No retiene por defecto | N/A |
| **OpenAI (API)** | 30 días | Sí, opt-out disponible |

> **Importante**: estos datos son orientativos para mediados de 2026. Verifica siempre las políticas actuales del proveedor.

### 4. Sanitizar datos de test

Si tus fixtures de test contienen datos reales de usuarios, sanitízalos antes de que el agente los lea:

```javascript
// MAL: datos reales en fixtures
const testUser = {
  name: "María García López",
  email: "maria.garcia@empresa-real.com",
  phone: "+34 612 345 678"
};

// BIEN: datos ficticios
const testUser = {
  name: "Test User",
  email: "test@example.com",
  phone: "+00 000 000 000"
};
```

### 5. Separar entornos

- **Desarrollo**: el agente puede acceder a código y configuraciones de desarrollo
- **Staging/producción**: el agente nunca debería tener acceso a entornos con datos reales
- **CI/CD**: si el agente participa en CI/CD, asegurar que no tiene acceso a secrets de producción

---

## Evaluación Rápida de Riesgo

Responde estas preguntas para evaluar tu nivel de riesgo:

```markdown
1. ¿Tu código fuente es confidencial?                    [ ] Sí  [ ] No
2. ¿Tu proyecto maneja datos personales (PII)?            [ ] Sí  [ ] No
3. ¿Estás en un sector regulado (banca, salud, gobierno)? [ ] Sí  [ ] No
4. ¿Tus fixtures de test contienen datos reales?          [ ] Sí  [ ] No
5. ¿Tienes secrets en archivos del proyecto?              [ ] Sí  [ ] No
6. ¿Tu empresa tiene política de seguridad de datos?      [ ] Sí  [ ] No

Resultado:
- 0-1 "Sí": Riesgo bajo — las prácticas estándar son suficientes
- 2-3 "Sí": Riesgo medio — implementa todas las mitigaciones de este capítulo
- 4-6 "Sí": Riesgo alto — consulta con seguridad/legal antes de usar IA en desarrollo
```

---

## Qué Hacer si ya Enviaste Datos Sensibles

Si accidentalmente pegaste un secret en un prompt:

1. **Rota el secret inmediatamente** (cambia la API key, password, token)
2. Verifica los logs de acceso del servicio asociado al secret
3. Documenta el incidente según tu política de seguridad
4. Configura las exclusiones del agente para que no vuelva a ocurrir

---

## Navegación

| | |
|---|---|
| **Anterior** | [01-propiedad-intelectual.md](01-propiedad-intelectual.md) |
| **Siguiente** | [03-compliance-regulacion.md](03-compliance-regulacion.md) |
| **Módulo** | [README del módulo](../README.md) |
