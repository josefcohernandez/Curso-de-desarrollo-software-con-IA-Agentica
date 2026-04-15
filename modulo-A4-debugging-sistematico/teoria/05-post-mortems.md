# Post-mortems asistidos por IA

## Para qué sirve un post-mortem

Un post-mortem no es un documento de culpas — es una herramienta de aprendizaje. Después de resolver un incidente, el post-mortem documenta qué pasó, por qué pasó, y qué haremos para que no vuelva a pasar.

Los agentes de código son excelentes asistentes para generar post-mortems porque:

- Pueden reconstruir el timeline a partir de commits, logs y diffs
- Pueden analizar la causa raíz de forma estructurada
- Pueden sugerir acciones preventivas basadas en el código
- Pueden proponer tests de regresión específicos

## Prompt template para post-mortem

```text
Acabamos de resolver un incidente. Ayúdame a escribir el post-mortem.

Contexto:
- Qué pasó: [resumen del incidente en 1-2 frases]
- Cuándo se detectó: [timestamp o momento]
- Cuándo se resolvió: [timestamp o momento]
- Impacto: [qué usuarios/funcionalidad se vio afectada]
- Los cambios que hicimos: [git diff o lista de commits del fix]

Genera un post-mortem con estas secciones:
1. Resumen ejecutivo (2-3 frases)
2. Timeline detallado
3. Causa raíz (técnica, no culpa personal)
4. Impacto (usuarios afectados, duración, datos perdidos)
5. Acciones correctivas (qué hicimos para resolverlo)
6. Acciones preventivas (qué haremos para que no se repita)
7. Lecciones aprendidas
```

## Estructura de un buen post-mortem

### Ejemplo generado

```markdown
## Post-mortem: Cobro incorrecto en pedidos con cupón de descuento

### Resumen ejecutivo
Entre las 14:00 y las 16:30 del 15 de marzo, los pedidos con cupón de 
descuento se cobraron por el precio completo sin descuento. 
Se estima que 23 pedidos fueron afectados por un total de 847 EUR 
cobrados de más. La causa fue un error de secuencia en el orderService 
donde el pago se creaba antes de aplicar el descuento.

### Timeline
| Hora | Evento |
|------|--------|
| 14:00 | Deploy de la feature de cupones (commit abc123) |
| 14:15 | Primer pedido con cupón — cobro incorrecto |
| 15:30 | Reporte del cliente: "me cobraron el precio completo" |
| 15:45 | Investigación iniciada — se confirma el bug |
| 16:15 | Causa raíz identificada — fix implementado |
| 16:30 | Deploy del fix (commit def456) — verificado en producción |
| 17:00 | Reembolsos procesados para los 23 pedidos afectados |

### Causa raíz
En `src/services/orderService.ts`, la función `createOrder()` ejecutaba:
1. Calcular total del pedido
2. Crear el pago con ese total ← el pago se creaba aquí
3. Aplicar el descuento del cupón
4. Actualizar el total del pedido ← pero el pago ya estaba creado

El descuento se guardaba correctamente en la tabla `orders`, pero la 
tabla `payments` registraba el monto sin descuento.

### Impacto
- 23 pedidos cobrados incorrectamente
- 847 EUR cobrados de más en total
- 2.5 horas de afectación
- 0 datos perdidos

### Acciones correctivas
- [x] Fix implementado: mover el cálculo del descuento antes de crear el pago
- [x] Reembolsos procesados para los 23 pedidos afectados
- [x] Comunicación enviada a los clientes afectados

### Acciones preventivas
- [ ] Añadir test de integración que verifique que el monto del pago
      coincide con el total del pedido después de descuentos
- [ ] Añadir constraint en la base de datos: payments.amount <= orders.total
- [ ] Añadir alerta de monitorización: si payments.amount != orders.total, 
      notificar al equipo

### Lecciones aprendidas
1. Los tests unitarios del cupón pasaban porque testeaban cada función 
   por separado — faltaba un test de integración del flujo completo
2. La secuencia de operaciones en un service es tan importante como 
   la lógica de cada operación individual
3. Necesitamos un checklist de revisión para features que involucran 
   flujos de pago
```

## Sugerir tests de regresión

Después del post-mortem, pide al agente tests específicos:

```text
Basándote en el post-mortem anterior, genera tests de regresión que:
1. Fallen con el código original (antes del fix)
2. Pasen con el código actual (después del fix)
3. Cubran variaciones del mismo problema
```

### Ejemplo de tests generados

```javascript
describe('Order with coupon - regression tests (incident 2024-03-15)', () => {
  test('payment amount should include coupon discount', async () => {
    const order = await orderService.createOrder({
      items: [{ productId: 'p1', quantity: 1, price: 100 }],
      couponCode: 'SAVE20'  // 20% de descuento
    });

    const payment = await paymentService.getByOrderId(order.id);
    
    expect(order.total).toBe(80);        // 100 - 20% = 80
    expect(payment.amount).toBe(80);      // El pago debe coincidir
    expect(payment.amount).toBe(order.total); // Invariante explícita
  });

  test('payment and order total should always match', async () => {
    // Test sin cupón (sanity check)
    const order = await orderService.createOrder({
      items: [{ productId: 'p1', quantity: 2, price: 50 }]
    });

    const payment = await paymentService.getByOrderId(order.id);
    expect(payment.amount).toBe(order.total);
  });
});
```

## Cuándo escribir un post-mortem

No todos los bugs merecen un post-mortem completo. Usa esta guía:

| Situación | ¿Post-mortem? | Nivel |
|-----------|--------------|-------|
| Bug en producción que afectó usuarios | Sí | Completo |
| Bug en producción detectado antes de impactar | Sí | Ligero (timeline + causa raíz + test) |
| Bug detectado en staging/QA | No | Solo test de regresión |
| Bug local durante desarrollo | No | Fix y seguir |

## El post-mortem como herramienta de mejora

Un post-mortem bien hecho alimenta el ciclo de mejora continua:

```text
Incidente → Post-mortem → Acciones preventivas → Mejor código/proceso
                                ↓
                    Tests de regresión → Mejor cobertura
                                ↓
                    Actualización de CLAUDE.md → Mejor contexto para el agente
```

Actualiza tu `CLAUDE.md` o archivo de instrucciones del agente con las lecciones aprendidas:

```markdown
## Lecciones de incidentes
- En flujos de pago: SIEMPRE calcular el total final antes de crear el payment
- Tests de integración obligatorios para cualquier feature que toque payments
```

---

[← Anterior: Debugging cross-stack](04-debugging-cross-stack.md) | [Volver al índice del módulo](../README.md)
