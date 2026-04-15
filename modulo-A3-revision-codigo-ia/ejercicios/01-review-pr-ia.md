# Ejercicio 1: Review de un PR generado por IA

## Contexto

Un desarrollador pidió a un agente de código: "Crea un endpoint POST /api/users que registre nuevos usuarios con validación de email y password, y envíe un email de bienvenida."

El agente generó el siguiente PR. Tu trabajo es hacer una code review exhaustiva.

## El diff del PR

### Archivo: `src/controllers/userController.ts` (nuevo)

```typescript
import { Request, Response } from 'express';
import { UserService } from '../services/userService';
import { sendEmail } from '../utils/emailHelper';
import { validateInput } from '../utils/validator';
import { Logger } from '../utils/logger';

const logger = new Logger('UserController');

export class UserController {
  private userService: UserService;

  constructor() {
    this.userService = new UserService();
  }

  async createUser(req: Request, res: Response) {
    try {
      const { name, email, password } = req.body;

      // Validate input
      validateInput({ name, email, password });

      // Check if user exists
      const existing = await this.userService.findByEmail(email);
      if (existing) {
        return res.status(400).json({ error: 'User already exists' });
      }

      // Create user
      const user = await this.userService.create({
        name,
        email,
        password,  // Store the password
        role: 'admin',
        createdAt: new Date()
      });

      // Send welcome email
      sendEmail(email, 'Welcome!', `Hello ${name}, welcome to our platform!`);

      logger.info(`User created: ${email}, password: ${password}`);

      return res.status(200).json({
        message: 'User created successfully',
        user: user
      });
    } catch (error) {
      console.log('Error creating user:', error);
      return res.status(500).json({ error: 'Internal server error' });
    }
  }
}
```

### Archivo: `src/services/userService.ts` (nuevo)

```typescript
import { db } from '../config/database';

export class UserService {
  async findByEmail(email: string) {
    const result = await db.query(
      `SELECT * FROM users WHERE email = '${email}'`
    );
    return result.rows[0];
  }

  async create(userData: any) {
    const result = await db.query(
      `INSERT INTO users (name, email, password, role, created_at)
       VALUES ('${userData.name}', '${userData.email}', '${userData.password}', '${userData.role}', '${userData.createdAt}')
       RETURNING *`
    );
    return result.rows[0];
  }

  async findById(id: string) {
    try {
      const result = await db.query(`SELECT * FROM users WHERE id = '${id}'`);
      return result.rows[0];
    } catch (e) {
      console.log(e);
      return null;
    }
  }
}
```

### Archivo: `src/utils/emailHelper.ts` (nuevo)

```typescript
import nodemailer from 'nodemailer';

const transporter = nodemailer.createTransport({
  host: 'smtp.gmail.com',
  port: 587,
  auth: {
    user: 'myapp@gmail.com',
    pass: 'MyP@ssw0rd123'
  }
});

export function sendEmail(to: string, subject: string, body: string) {
  transporter.sendMail({
    from: 'myapp@gmail.com',
    to,
    subject,
    html: body
  });
}
```

### Archivo: `src/utils/validator.ts` (nuevo)

```typescript
export function validateInput(data: any) {
  if (!data.name) throw new Error('Name is required');
  if (!data.email) throw new Error('Email is required');
  if (!data.password) throw new Error('Password is required');
  // TODO: add email format validation
  // TODO: add password strength validation
}
```

### Archivo: `tests/userController.test.ts` (nuevo)

```typescript
import { UserController } from '../src/controllers/userController';

describe('UserController', () => {
  test('should create user', async () => {
    const controller = new UserController();
    const req = { body: { name: 'Test', email: 'test@test.com', password: '123' } };
    const res = { status: jest.fn().mockReturnThis(), json: jest.fn() };
    await controller.createUser(req as any, res as any);
    expect(res.status).toHaveBeenCalled();
  });

  test('should handle errors', async () => {
    const controller = new UserController();
    const req = { body: {} };
    const res = { status: jest.fn().mockReturnThis(), json: jest.fn() };
    await controller.createUser(req as any, res as any);
    expect(res.status).toHaveBeenCalled();
  });
});
```

## Tu tarea

1. **Revisa el PR completo** usando el checklist de prioridades de la teoría
2. **Documenta cada hallazgo** clasificándolo como Crítico, Alto, Medio o Bajo
3. **Para cada hallazgo**, indica: qué archivo, qué línea (aprox), qué problema y cómo corregirlo

## Formato de entrega

Completa esta tabla con tus hallazgos (debería haber al menos 8):

| # | Prioridad | Archivo | Problema | Corrección propuesta |
|---|-----------|---------|----------|---------------------|
| 1 | | | | |
| 2 | | | | |
| ... | | | | |

## Criterios de evaluación

| Nivel | Requisito |
|-------|-----------|
| **Aprobado** | Identificar al menos 5 problemas incluyendo los 3 críticos |
| **Notable** | Identificar al menos 8 problemas en todos los niveles de prioridad |
| **Excelente** | Identificar 10+ problemas con correcciones específicas y justificadas |

## Pistas

Si te atascas, recuerda las categorías del checklist:
- **Crítico**: seguridad, dependencias, secrets
- **Alto**: lógica de negocio, regresiones, edge cases
- **Medio**: over-engineering, inconsistencia, tests
- **Bajo**: estilo, comentarios

---

[Siguiente ejercicio: Encontrar la regresión →](02-encontrar-regresion.md)
