# Ejercicio 3: Poda de complejidad

## Contexto

Un agente de código recibió este prompt: "Crea un sistema de notificaciones que envíe emails cuando un pedido cambia de estado."

El agente generó una solución correcta pero excesivamente compleja. Tu trabajo es **simplificarla manteniendo la misma funcionalidad**, reduciendo al menos un 30% de las líneas.

## Código generado por el agente

```typescript
// src/notifications/types.ts
export interface INotification {
  type: string;
  recipient: string;
  subject: string;
  body: string;
  metadata?: Record<string, unknown>;
}

export interface INotificationChannel {
  send(notification: INotification): Promise<boolean>;
  getName(): string;
  isAvailable(): Promise<boolean>;
}

export interface INotificationStrategy {
  selectChannels(notification: INotification): INotificationChannel[];
}

export interface INotificationFormatter {
  format(template: string, data: Record<string, unknown>): string;
}

export interface INotificationValidator {
  validate(notification: INotification): ValidationResult;
}

export interface ValidationResult {
  isValid: boolean;
  errors: string[];
}

// src/notifications/channels/emailChannel.ts
import { INotificationChannel, INotification } from '../types';
import { EmailService } from '../../services/emailService';

export class EmailChannel implements INotificationChannel {
  private emailService: EmailService;

  constructor(emailService: EmailService) {
    this.emailService = emailService;
  }

  async send(notification: INotification): Promise<boolean> {
    try {
      await this.emailService.send({
        to: notification.recipient,
        subject: notification.subject,
        body: notification.body
      });
      return true;
    } catch (error) {
      return false;
    }
  }

  getName(): string {
    return 'email';
  }

  async isAvailable(): Promise<boolean> {
    return true;
  }
}

// src/notifications/strategies/defaultStrategy.ts
import { INotificationStrategy, INotification, INotificationChannel } from '../types';

export class DefaultNotificationStrategy implements INotificationStrategy {
  private channels: INotificationChannel[];

  constructor(channels: INotificationChannel[]) {
    this.channels = channels;
  }

  selectChannels(notification: INotification): INotificationChannel[] {
    return this.channels.filter(ch => ch.getName() === 'email');
  }
}

// src/notifications/formatters/templateFormatter.ts
import { INotificationFormatter } from '../types';

export class TemplateFormatter implements INotificationFormatter {
  format(template: string, data: Record<string, unknown>): string {
    let result = template;
    for (const [key, value] of Object.entries(data)) {
      result = result.replace(new RegExp(`\\{\\{${key}\\}\\}`, 'g'), String(value));
    }
    return result;
  }
}

// src/notifications/validators/notificationValidator.ts
import { INotificationValidator, INotification, ValidationResult } from '../types';

export class NotificationValidator implements INotificationValidator {
  validate(notification: INotification): ValidationResult {
    const errors: string[] = [];
    if (!notification.recipient) errors.push('Recipient is required');
    if (!notification.subject) errors.push('Subject is required');
    if (!notification.body) errors.push('Body is required');
    return { isValid: errors.length === 0, errors };
  }
}

// src/notifications/notificationManager.ts
import {
  INotification,
  INotificationStrategy,
  INotificationFormatter,
  INotificationValidator
} from './types';

export class NotificationManager {
  private strategy: INotificationStrategy;
  private formatter: INotificationFormatter;
  private validator: INotificationValidator;

  constructor(
    strategy: INotificationStrategy,
    formatter: INotificationFormatter,
    validator: INotificationValidator
  ) {
    this.strategy = strategy;
    this.formatter = formatter;
    this.validator = validator;
  }

  async notify(notification: INotification): Promise<boolean> {
    const validation = this.validator.validate(notification);
    if (!validation.isValid) {
      throw new Error(`Invalid notification: ${validation.errors.join(', ')}`);
    }

    const channels = this.strategy.selectChannels(notification);
    const results = await Promise.all(
      channels.map(channel => channel.send(notification))
    );

    return results.every(r => r === true);
  }
}

// src/notifications/factory.ts
import { NotificationManager } from './notificationManager';
import { EmailChannel } from './channels/emailChannel';
import { DefaultNotificationStrategy } from './strategies/defaultStrategy';
import { TemplateFormatter } from './formatters/templateFormatter';
import { NotificationValidator } from './validators/notificationValidator';
import { EmailService } from '../services/emailService';

export class NotificationFactory {
  static create(emailService: EmailService): NotificationManager {
    const emailChannel = new EmailChannel(emailService);
    const strategy = new DefaultNotificationStrategy([emailChannel]);
    const formatter = new TemplateFormatter();
    const validator = new NotificationValidator();
    return new NotificationManager(strategy, formatter, validator);
  }
}

// src/handlers/orderStatusHandler.ts
import { NotificationFactory } from '../notifications/factory';
import { EmailService } from '../services/emailService';

const emailService = new EmailService();
const notificationManager = NotificationFactory.create(emailService);

const ORDER_TEMPLATES: Record<string, { subject: string; body: string }> = {
  confirmed: {
    subject: 'Pedido confirmado',
    body: 'Tu pedido #{{orderId}} ha sido confirmado.'
  },
  shipped: {
    subject: 'Pedido enviado',
    body: 'Tu pedido #{{orderId}} está en camino. Tracking: {{tracking}}'
  },
  delivered: {
    subject: 'Pedido entregado',
    body: 'Tu pedido #{{orderId}} ha sido entregado.'
  }
};

export async function onOrderStatusChange(
  orderId: string,
  newStatus: string,
  userEmail: string,
  metadata?: Record<string, unknown>
) {
  const template = ORDER_TEMPLATES[newStatus];
  if (!template) return;

  const formatter = new (await import('../notifications/formatters/templateFormatter'))
    .TemplateFormatter();

  const data = { orderId, ...metadata };

  await notificationManager.notify({
    type: 'order_status',
    recipient: userEmail,
    subject: formatter.format(template.subject, data),
    body: formatter.format(template.body, data),
    metadata: data
  });
}
```

## Tu tarea

1. **Analiza el código**: identifica qué abstracciones son innecesarias para el requisito actual (solo email, solo pedidos)
2. **Reescríbelo**: mantén la misma funcionalidad pero elimina la complejidad innecesaria
3. **Mide la reducción**: cuenta las líneas antes y después

## Restricciones

- La funcionalidad debe ser idéntica: enviar emails cuando un pedido cambia de estado
- Los templates de email deben seguir siendo configurables
- La validación básica debe mantenerse
- El código resultante debe ser fácil de extender en el futuro (pero sin construir esa extensibilidad hoy)

## Criterios de evaluación

| Nivel | Requisito |
|-------|-----------|
| **Aprobado** | Reducir al menos 30% de líneas manteniendo funcionalidad |
| **Notable** | Reducir 40%+ y que el código sea más legible que el original |
| **Excelente** | Reducir 50%+, código limpio, y con un comentario que explique cómo extenderlo si fuera necesario en el futuro |

## Preguntas guía

Antes de reescribir, responde:

1. ¿Cuántas interfaces son necesarias para enviar un email? ¿Cuántas hay?
2. ¿El `TemplateFormatter` se usa en más de un sitio? ¿Necesita ser una clase?
3. ¿El `NotificationValidator` justifica su propia clase para 3 comprobaciones?
4. ¿La `NotificationFactory` aporta valor si solo hay un tipo de canal?
5. ¿La `DefaultNotificationStrategy` hace algo que no sea filtrar por 'email'?

---

[← Ejercicio anterior](02-encontrar-regresion.md) | [Siguiente ejercicio: Red flags en tests →](04-red-flags-tests.md)
