# Escenario: Incidente en Producción

## El problema

Llega una alerta a las 3 AM. El servicio de pagos devuelve errores 500. Los usuarios no pueden completar transacciones. Hay presión real para resolverlo rápido — cada minuto de downtime tiene un coste medible.

En un incidente, la velocidad importa. Un agente de código no toma decisiones por ti, pero puede **investigar en paralelo** mientras tú piensas: analizar logs, revisar commits recientes y verificar hipótesis en segundos.

---

## Workflow en 5 pasos

### Paso 1: Triage (5 minutos)

El objetivo es entender el alcance y encontrar pistas iniciales. No implementes nada todavía.

```text
Urgente: el endpoint POST /api/payments devuelve 500 desde las 02:47.
Aquí están los logs de la última hora:

[2024-03-15 02:47:12] ERROR PaymentService.process: TypeError: Cannot read 
  property 'amount' of undefined
[2024-03-15 02:47:12] ERROR   at processPayment (src/services/payment.js:47)
[2024-03-15 02:47:13] ERROR   at Router.handle (src/routes/payments.js:23)

¿Qué cambió? Revisa los commits de las últimas 24 horas con git log --oneline --since="24 hours ago".
```

**Qué hacer con la respuesta**: el agente te dará una lista de commits y una hipótesis sobre la causa. No actúes aún — primero confirma el diagnóstico.

### Paso 2: Diagnóstico (10 minutos)

Ahora confirmas la causa raíz. Pide al agente que investigue el commit sospechoso:

```text
El último deploy fue a las 02:30 (commit abc123).
Muéstrame el diff de ese commit y dime si algún cambio 
puede causar un 500 en el endpoint de pagos.
Busca específicamente: cambios en el formato de datos,
renombrado de campos, y cambios en dependencias.
```

**Árbol de diagnóstico rápido**:

```text
¿El error empezó tras un deploy?
├── Sí → Revisar diff del último commit
│   ├── Cambio obvio → Ir al paso 3 (fix)
│   └── No es obvio → Ampliar investigación a últimos 5 commits
└── No → Investigar factores externos
    ├── ¿Cambió alguna dependencia externa (API, DB)?
    ├── ¿Hay problemas de infraestructura (disco, memoria, red)?
    └── ¿Hay un pico de tráfico inusual?
```

**Consejo**: pide al agente que investigue varias hipótesis en paralelo. No esperes a que descarte una antes de explorar la siguiente.

### Paso 3: Fix o rollback (10 minutos)

Aquí tomas la decisión más importante del incidente. Es un árbol de decisión, no un prompt:

| Situación | Acción | Riesgo |
|-----------|--------|--------|
| La causa es clara y el fix es de 1-5 líneas | Implementar fix con el agente | Bajo — cambio localizado |
| La causa es clara pero el fix es complejo | Rollback al commit anterior | Bajo — recuperas estado funcional |
| La causa no está clara | Rollback inmediato + investigar después | Medio — el rollback puede no resolver si la causa es externa |
| El rollback no es posible (migración de DB) | Fix forward con el agente | Alto — requiere la máxima atención |

Si decides implementar el fix:

```text
El bug está en src/services/payment.js línea 47.
El campo se renombró de 'amount' a 'total_amount' en el commit abc123
pero no se actualizó el PaymentService.
Corrige la referencia y ejecuta los tests de integración del módulo de pagos.
```

**Regla de oro del incidente**: si no puedes resolver en 10 minutos, haz rollback. Siempre puedes investigar con calma después.

### Paso 4: Validación (5 minutos)

Nunca des un incidente por resuelto sin validar:

```text
Ejecuta los tests de integración del módulo de pagos.
Verifica que el endpoint POST /api/payments responde 200 
con un payload válido de ejemplo:
{
  "user_id": "test-user-1",
  "total_amount": 99.99,
  "currency": "EUR"
}
```

**Checklist de validación**:

- [ ] El endpoint devuelve 200 con datos válidos
- [ ] Los tests de integración pasan
- [ ] Los logs ya no muestran errores 500
- [ ] Otros endpoints relacionados siguen funcionando (tests de regresión)

### Paso 5: Post-mortem (después del incidente)

Cuando la emergencia termina, documenta lo ocurrido. El agente es excelente para esto:

```text
Genera un documento de post-mortem con esta estructura:
- Resumen del incidente (1 párrafo)
- Timeline (tabla con hora y evento)
- Causa raíz
- Impacto (duración, usuarios afectados, transacciones fallidas)
- Resolución aplicada
- Acciones preventivas (qué haremos para que no vuelva a pasar)
```

---

## Lecciones clave

1. **La velocidad importa**: en un incidente no hay tiempo para prompts perfectos ni sesiones de exploración largas. Ve al grano
2. **El agente investiga, tú decides**: nunca delegues la decisión de fix vs. rollback al agente. Es tu responsabilidad
3. **Investigación paralela**: mientras tú analizas los logs, el agente puede revisar el git log y los diffs simultáneamente
4. **Rollback es siempre una opción válida**: no hay vergüenza en hacer rollback. Es la decisión más segura cuando la causa no está clara
5. **El post-mortem es obligatorio**: sin documentación, el mismo incidente ocurrirá otra vez. El agente hace la escritura menos dolorosa

---

## Anti-patrones en incidentes

| Anti-patrón | Por qué es peligroso |
|-------------|---------------------|
| Investigar 30 minutos antes de actuar | Los usuarios siguen afectados mientras tú analizas |
| Hacer fix sin tests de validación | Puedes introducir un segundo bug encima del primero |
| No hacer rollback "porque el fix debe ser fácil" | Subestimar la complejidad bajo presión es habitual |
| Pedir al agente que "arregle el incidente" sin dar contexto | Prompt vago = acción impredecible en el peor momento |
| Saltar el post-mortem | Sin documentación, repetirás el mismo error |

---

## Conexión con otros módulos

- El workflow de debugging sistemático del [Módulo A4](../../modulo-A4-debugging-sistematico/README.md) es la base de los pasos 1 y 2
- La revisión rápida del diff usa las técnicas del [Módulo A3](../../modulo-A3-revision-codigo-ia/README.md)
- Para el post-mortem, aplicar las técnicas de prompting del [Módulo A1](../../modulo-A1-prompting-efectivo/README.md) al prompt de documentación
