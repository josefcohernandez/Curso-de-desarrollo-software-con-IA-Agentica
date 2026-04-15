# Propiedad Intelectual y Copyright de Código IA

## Introducción

¿De quién es el código que genera un agente de IA? ¿Puedes usarlo libremente en un proyecto comercial? ¿Qué pasa si el agente reproduce código de un proyecto open source? Estas preguntas no tienen respuestas definitivas todavía, pero como developer profesional necesitas un marco práctico para tomar decisiones.

---

## El Estado Legal (Mediados de 2026)

### Lo que sabemos

- **No hay consenso legal global** sobre la propiedad del código generado por IA
- Las leyes de copyright fueron diseñadas para obras creadas por humanos; la IA cae en un vacío legal en la mayoría de jurisdicciones
- Varios tribunales en EE.UU. y la UE están resolviendo casos que sentarán precedentes

### La tendencia

El consenso emergente es que el código generado por IA siguiendo instrucciones específicas de un humano se considera **obra derivada del humano** que proporcionó las instrucciones. Es decir:

- Si escribes un prompt detallado con especificaciones, restricciones y verificaciones
- Y el agente genera código en respuesta a esas instrucciones
- Y tú revisas, editas y apruebas el resultado

Entonces el código resultante se trata como tu obra, de la misma forma que el código que escribirías con ayuda de Stack Overflow o documentación.

### El riesgo real

El riesgo no está en la generación — está en la **reproducción**. Si el agente genera código que es una copia sustancial de un proyecto existente (especialmente uno con licencia restrictiva como GPL), puedes tener un problema de copyright con el autor original.

---

## Implicaciones Prácticas

### Para proyectos comerciales

| Aspecto | Recomendación |
|---------|---------------|
| Uso general de código IA | Permitido — trátalo como código tuyo que debes revisar |
| Reproducción literal | Riesgo — verifica que no sea copia de proyectos con licencia restrictiva |
| Licencia de la herramienta | Lee los ToS — la mayoría te otorgan derechos sobre el output |
| Patentes | Zona gris — consultar con legal si pretendes patentar código IA |

### Para proyectos open source

| Aspecto | Recomendación |
|---------|---------------|
| Contribuciones a proyectos existentes | Verifica la política del proyecto sobre código IA |
| Tu propio proyecto open source | Puedes usar código IA, pero sé transparente si el proyecto lo requiere |
| Compatibilidad de licencias | Si el agente genera código que parece venir de un proyecto GPL, no lo uses en un proyecto MIT sin verificar |

### Para proyectos enterprise

- Consultar con el departamento legal antes de adoptar cualquier herramienta de IA para desarrollo
- Documentar qué herramienta se usa y bajo qué términos
- Verificar que los términos de servicio del proveedor son compatibles con tus acuerdos con clientes

---

## Licencias de las Herramientas Principales

| Herramienta | Política sobre el output (resumida) |
|-------------|-------------------------------------|
| **Claude Code** | El output te pertenece. Anthropic no reclama derechos. Sujeto a Terms of Service |
| **GitHub Copilot** | El output te pertenece. Opción de filtrar sugerencias que coincidan con código público |
| **Cursor** | Similar a Copilot — el output te pertenece |
| **Cline** | Open source — el output depende del proveedor LLM que configures |

> **Importante**: estas políticas pueden cambiar. Revisa los Terms of Service actuales antes de tomar decisiones.

---

## Detección de Código Reproducido

### Señales de que el agente puede estar reproduciendo código existente

- Genera un bloque largo (>20 líneas) que parece muy "pulido" y completo
- Incluye comentarios con copyright o licencia de otro proyecto
- Nombres de variables o funciones muy específicos que no provienen de tu prompt
- Patrones idénticos a librerías conocidas

### Qué hacer si sospechas reproducción

1. Busca fragmentos del código en GitHub (usa la búsqueda de código)
2. Si encuentras una coincidencia, verifica la licencia del proyecto original
3. Si la licencia es permisiva (MIT, Apache 2.0), puedes usar el código con atribución
4. Si la licencia es copyleft (GPL, AGPL), no puedes usar el código en un proyecto con licencia diferente sin cumplir los términos

---

## La Regla Práctica

> **Trata el código generado por IA como un borrador que tú refinaste. La responsabilidad final del código que entra en producción siempre es tuya.**

Esto significa:
- Tú firmas los commits, tú haces el merge, tú respondes por el código
- Si el código tiene un bug, es tu bug — no "culpa de la IA"
- Si el código viola una licencia, es tu violación
- Si el código tiene una vulnerabilidad, es tu responsabilidad

Esto no es diferente de copiar código de Stack Overflow: la responsabilidad de verificar, adaptar y mantener siempre fue tuya.

---

## Resumen

| Pregunta | Respuesta práctica |
|----------|-------------------|
| ¿Puedo usar código IA en mi proyecto comercial? | Sí, con revisión y bajo tu responsabilidad |
| ¿De quién es el código? | Tuyo (en la mayoría de jurisdicciones y según los ToS de las herramientas) |
| ¿Qué riesgo hay? | Reproducción de código con licencia restrictiva |
| ¿Cómo mitigo el riesgo? | Revisar código sospechoso, buscar coincidencias, verificar licencias |
| ¿Necesito consultar con legal? | Sí, si trabajas en enterprise o manejas IP sensible |

---

## Navegación

| | |
|---|---|
| **Siguiente** | [02-privacidad-datos.md](02-privacidad-datos.md) |
| **Módulo** | [README del módulo](../README.md) |
