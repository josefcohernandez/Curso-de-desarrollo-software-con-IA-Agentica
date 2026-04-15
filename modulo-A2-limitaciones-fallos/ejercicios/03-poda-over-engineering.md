# Ejercicio 3: Poda de Over-engineering

## Objetivo

Simplificar un código excesivamente complejo generado por IA — una "fábrica de notificaciones" con Strategy pattern, múltiples clases y un builder — que en la práctica solo necesita enviar un email. Reducir de ~80 líneas a ~30 manteniendo la misma funcionalidad.

**Tiempo estimado: 15 minutos**

---

## El Código Over-engineered

Un desarrollador pidió: "Crea una función para enviar un email de bienvenida cuando un usuario se registra." El agente generó esto:

```javascript
// notification-system.js — generado por IA

// Interfaz base para proveedores de notificación
class NotificationProvider {
  async send(recipient, subject, body) {
    throw new Error("Method not implemented");
  }
  getName() {
    throw new Error("Method not implemented");
  }
}

// Implementación para email
class EmailNotificationProvider extends NotificationProvider {
  constructor(smtpConfig) {
    super();
    this.smtpConfig = smtpConfig;
    this.transport = this.createTransport();
  }

  createTransport() {
    const nodemailer = require("nodemailer");
    return nodemailer.createTransport(this.smtpConfig);
  }

  async send(recipient, subject, body) {
    return this.transport.sendMail({
      from: this.smtpConfig.from,
      to: recipient,
      subject: subject,
      html: body
    });
  }

  getName() {
    return "email";
  }
}

// Factory para crear el proveedor correcto
class NotificationFactory {
  static providers = new Map();

  static register(name, ProviderClass) {
    NotificationFactory.providers.set(name, ProviderClass);
  }

  static create(name, config) {
    const ProviderClass = NotificationFactory.providers.get(name);
    if (!ProviderClass) {
      throw new Error(`Provider "${name}" not registered`);
    }
    return new ProviderClass(config);
  }
}

// Builder para construir notificaciones
class NotificationBuilder {
  constructor() {
    this.recipient = null;
    this.subject = null;
    this.body = null;
    this.providerName = "email";
  }

  to(recipient) { this.recipient = recipient; return this; }
  withSubject(subject) { this.subject = subject; return this; }
  withBody(body) { this.body = body; return this; }
  via(providerName) { this.providerName = providerName; return this; }

  async send(config) {
    if (!this.recipient || !this.subject || !this.body) {
      throw new Error("Notification incomplete: recipient, subject, and body required");
    }
    const provider = NotificationFactory.create(this.providerName, config);
    return provider.send(this.recipient, this.subject, this.body);
  }
}

// Registrar proveedores disponibles
NotificationFactory.register("email", EmailNotificationProvider);

// Función de envío de bienvenida
async function sendWelcomeEmail(userEmail, userName, smtpConfig) {
  const notification = new NotificationBuilder()
    .to(userEmail)
    .withSubject("Bienvenido")
    .withBody(`<h1>Hola ${userName}</h1><p>Gracias por registrarte.</p>`)
    .via("email");

  return notification.send(smtpConfig);
}

module.exports = { sendWelcomeEmail, NotificationFactory, NotificationBuilder };
```

---

## Tu Tarea

1. **Analiza el código**: identifica qué partes son necesarias y cuáles son abstracciones innecesarias para el requisito real (enviar UN email de bienvenida).

2. **Simplifica**: reescribe el código manteniendo exactamente la misma funcionalidad externa (`sendWelcomeEmail` que acepta email, nombre y config SMTP, y envía un email HTML).

3. **Objetivo**: reducir a ~30 líneas o menos.

Escribe tu versión simplificada aquí:

```javascript
// notification.js — tu versión simplificada

// TU CÓDIGO AQUÍ
```

---

## Evaluación

Verifica tu solución con estas preguntas:

| Criterio | Sí/No |
|----------|-------|
| ¿`sendWelcomeEmail(email, name, config)` sigue funcionando igual? | |
| ¿Eliminaste la clase base `NotificationProvider`? | |
| ¿Eliminaste el `NotificationFactory`? | |
| ¿Eliminaste el `NotificationBuilder`? | |
| ¿Tu código tiene 30 líneas o menos? | |
| ¿Si mañana necesitas SMS, puedes añadirlo sin refactorizar todo? | |

---

## Reflexión

Cuenta las líneas:
- **Original**: ~80 líneas, 5 clases/constructores, 1 patrón Factory, 1 patrón Builder, 1 patrón Strategy
- **Tu versión**: ¿cuántas líneas? ¿Cuántas clases?
- **Líneas eliminadas**: la diferencia son líneas que tendrías que mantener, testear y debuggear sin necesidad

La pregunta clave ante cualquier abstracción generada por IA:

> "¿Existe hoy más de una implementación? Si no, esta abstracción es prematura."

Si solo envías emails, no necesitas un sistema genérico de notificaciones. El día que necesites SMS, **ese es el momento** de abstraer — no antes.
