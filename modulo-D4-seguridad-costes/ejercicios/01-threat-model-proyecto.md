# Ejercicio 1: Threat Model STRIDE de una API de E-Commerce

## Objetivo

Realizar un threat model completo usando el framework STRIDE sobre una API de e-commerce, asistido por un agente de IA. Producir un informe de amenazas con impacto, probabilidad y mitigaciones accionables.

---

## Arquitectura del Proyecto

Vas a analizar la siguiente arquitectura (ficticia pero realista):

```text
┌─────────────┐     ┌──────────────┐     ┌──────────────┐
│   Frontend   │────▶│  API Gateway │────▶│   Auth API   │
│   (React)    │     │  (Express)   │     │   (JWT)      │
└─────────────┘     └──────┬───────┘     └──────────────┘
                           │
                    ┌──────┴───────┐
                    │              │
              ┌─────▼─────┐ ┌─────▼─────┐
              │  Products  │ │  Orders   │
              │  Service   │ │  Service  │
              └─────┬─────┘ └─────┬─────┘
                    │              │
              ┌─────▼─────┐ ┌─────▼─────┐
              │ PostgreSQL │ │ PostgreSQL │
              │ (products) │ │ (orders)  │
              └───────────┘ └───────────┘
                    
              ┌───────────┐ ┌───────────┐
              │    S3      │ │  Stripe   │
              │ (imágenes) │ │ (webhook) │
              └───────────┘ └───────────┘
```

### Componentes

- **Frontend React**: SPA que consume la API, autenticación con JWT almacenado en localStorage
- **API Gateway (Express)**: punto de entrada único, valida JWT, enruta a servicios
- **Auth API**: registro, login, recuperación de contraseña, emisión de JWT
- **Products Service**: CRUD de productos, búsqueda, categorías, upload de imágenes a S3
- **Orders Service**: crear pedido, listar pedidos del usuario, estados de pedido
- **Stripe Webhook**: recibe notificaciones de pago y actualiza el estado del pedido
- **S3**: almacenamiento de imágenes de productos

---

## Parte 1: Análisis STRIDE (20 min)

Pide al agente que analice la arquitectura usando STRIDE:

```text
Analiza la siguiente arquitectura de e-commerce usando el framework STRIDE.
[pegar la arquitectura de arriba]

Para cada categoría (Spoofing, Tampering, Repudiation, Information Disclosure, 
Denial of Service, Elevation of Privilege), identifica al menos 2 amenazas 
específicas a esta arquitectura (no amenazas genéricas).

Para cada amenaza proporciona:
1. Descripción concreta (qué puede hacer el atacante)
2. Componente afectado
3. Impacto (Crítico/Alto/Medio/Bajo)
4. Probabilidad (Alta/Media/Baja)
5. Mitigación recomendada con ejemplo de código o configuración
```

### Tabla de resultados esperados

Completa esta tabla con los hallazgos del agente:

| Cat. | Amenaza | Componente | Impacto | Probabilidad | Mitigación |
|------|---------|------------|---------|--------------|------------|
| S | | | | | |
| S | | | | | |
| T | | | | | |
| T | | | | | |
| R | | | | | |
| R | | | | | |
| I | | | | | |
| I | | | | | |
| D | | | | | |
| D | | | | | |
| E | | | | | |
| E | | | | | |

---

## Parte 2: Attack Surface Mapping (10 min)

Pide al agente que mapee la superficie de ataque:

```text
Genera un mapa de la superficie de ataque de esta API de e-commerce.
Lista todos los puntos de entrada (endpoints, uploads, webhooks, etc.) 
y para cada uno indica:
- ¿Requiere autenticación?
- ¿Qué datos recibe del exterior?
- ¿Se validan esos datos?
- Nivel de riesgo
```

---

## Parte 3: Priorización y Plan de Acción (10 min)

Con los hallazgos de las partes 1 y 2, crea un plan de acción priorizado:

```text
De todas las amenazas identificadas, ordénalas por prioridad 
(impacto × probabilidad). Selecciona las 5 más urgentes y para 
cada una:
1. Describe el fix específico
2. Estima el esfuerzo (horas)
3. Indica si el agente puede implementar el fix o requiere 
   decisiones humanas
```

---

## Entregable

Un documento de threat model con:

1. Tabla STRIDE completa (mínimo 12 amenazas: 2 por categoría)
2. Mapa de attack surface (mínimo 8 puntos de entrada)
3. Top 5 amenazas priorizadas con plan de acción
4. Reflexión: ¿el agente encontró amenazas que tú no habías considerado?

---

## Criterios de Evaluación

- Las amenazas son específicas a la arquitectura descrita, no genéricas
- Cada amenaza tiene los 5 campos completos (descripción, componente, impacto, probabilidad, mitigación)
- El plan de acción prioriza correctamente (impacto alto + probabilidad alta = máxima prioridad)
- Se identifican amenazas en al menos 4 componentes diferentes
