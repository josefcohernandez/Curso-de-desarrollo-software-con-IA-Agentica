# Ejercicio: Análisis de Compliance

## Objetivo

Diseñar una política de uso de IA en desarrollo para una empresa fintech que maneja datos PCI-DSS, aplicando los requisitos de compliance del [módulo de teoría](../teoria/03-compliance-regulacion.md).

**Tiempo estimado: 15 minutos**

---

## Escenario

Eres el CTO de **PayQuick**, una fintech que procesa pagos con tarjeta de crédito. El equipo de desarrollo (12 personas) quiere adoptar herramientas de IA para coding. Tu empresa tiene:

- Certificación PCI-DSS Level 2
- Certificación SOC 2 Type II
- Clientes en la UE (GDPR aplica)
- Repositorios privados en GitHub Enterprise
- Entorno de producción en AWS con datos de tarjetas encriptados

El equipo propone usar Claude Code con plan Max ($100/mes por developer).

---

## Instrucciones

### Parte 1: Análisis de Regulaciones

Identifica las regulaciones aplicables y sus requisitos para este escenario:

```markdown
## Regulaciones Aplicables

### PCI-DSS
- Requisito aplicable: ___
- Impacto en el uso de IA: ___
- Control necesario: ___

### SOC 2
- Requisito aplicable: ___
- Impacto en el uso de IA: ___
- Control necesario: ___

### GDPR
- Requisito aplicable: ___
- Impacto en el uso de IA: ___
- Control necesario: ___
```

### Parte 2: Política de Uso

Redacta una política que cubra:

1. **Alcance**: qué repositorios/módulos pueden usar IA y cuáles no
2. **Datos prohibidos**: qué datos nunca deben enviarse al agente
3. **Revisión obligatoria**: qué tipo de código requiere revisión manual adicional
4. **Logging y auditoría**: qué se registra sobre el uso de IA
5. **Excepciones**: proceso para solicitar excepciones a la política

### Parte 3: Configuración Técnica

Define la configuración técnica necesaria:

```markdown
## Configuración Técnica

### Permisos del agente (settings.json)
- Archivos/directorios denegados: ___
- Comandos denegados: ___
- Comandos permitidos: ___

### CI/CD
- Scans de seguridad obligatorios: ___
- Gates de aprobación: ___

### Proveedor
- Opción de deployment elegida: ___
- Retención de datos: ___
- Región de procesamiento: ___
```

---

## Criterios de Éxito

```markdown
- [ ] Las 3 regulaciones (PCI-DSS, SOC 2, GDPR) están analizadas
- [ ] La política tiene las 5 secciones (alcance, datos, revisión, logging, excepciones)
- [ ] Los módulos que manejan datos de tarjetas están excluidos o con restricciones especiales
- [ ] Hay un mecanismo de logging que no registra contenido de código sensible
- [ ] Se ha elegido una opción de deployment que cumple con los requisitos
- [ ] La política es implementable (no es un documento teórico sin acciones concretas)
```

---

## Reflexión

- ¿Es posible usar IA de forma productiva en un entorno PCI-DSS o las restricciones lo hacen impracticable?
- ¿Qué trade-offs estás haciendo entre seguridad y productividad?
- ¿Cómo evolucionaría esta política si el proveedor ofreciera deployment on-premise?

---

## Navegación

| | |
|---|---|
| **Ejercicio anterior** | [01-auditoria-privacidad.md](01-auditoria-privacidad.md) |
| **Siguiente ejercicio** | [03-comparativa-practica.md](03-comparativa-practica.md) |
| **Módulo** | [README del módulo](../README.md) |
