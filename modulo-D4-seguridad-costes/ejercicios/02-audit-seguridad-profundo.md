# Ejercicio 2: Security Review Profundo de un Módulo de Autenticación

## Objetivo

Realizar un security review profundo de un módulo de autenticación usando un agente de IA. El módulo tiene vulnerabilidades reales (no teóricas) que debes encontrar. El objetivo es encontrar al menos 3 vulnerabilidades explotables y proponer fixes concretos.

---

## El Módulo a Auditar

```javascript
// auth/controller.js
const bcrypt = require("bcrypt");
const jwt = require("jsonwebtoken");
const db = require("../db");

const JWT_SECRET = "mi-secreto-super-seguro-2024";
const JWT_EXPIRY = "30d";

async function register(req, res) {
  const { email, password, name } = req.body;

  // Verificar si el usuario ya existe
  const existing = await db.query(
    `SELECT id FROM users WHERE email = '${email}'`
  );
  if (existing.rows.length > 0) {
    return res.status(409).json({ error: "Email already registered" });
  }

  const hashedPassword = await bcrypt.hash(password, 8);
  const result = await db.query(
    `INSERT INTO users (email, password, name) VALUES ('${email}', '${hashedPassword}', '${name}') RETURNING id`
  );

  const token = jwt.sign({ userId: result.rows[0].id }, JWT_SECRET, {
    expiresIn: JWT_EXPIRY,
  });

  res.status(201).json({ token, userId: result.rows[0].id });
}

async function login(req, res) {
  const { email, password } = req.body;

  const result = await db.query(
    `SELECT * FROM users WHERE email = '${email}'`
  );

  if (result.rows.length === 0) {
    return res.status(401).json({ error: "User not found" });
  }

  const user = result.rows[0];
  const valid = await bcrypt.compare(password, user.password);

  if (!valid) {
    return res.status(401).json({ error: "Invalid password" });
  }

  const token = jwt.sign(
    { userId: user.id, role: user.role },
    JWT_SECRET,
    { expiresIn: JWT_EXPIRY }
  );

  console.log(`User ${email} logged in with token: ${token}`);

  res.json({ token, user: { id: user.id, email: user.email, name: user.name, role: user.role } });
}

async function resetPassword(req, res) {
  const { email } = req.body;

  const result = await db.query(
    `SELECT id FROM users WHERE email = '${email}'`
  );

  if (result.rows.length === 0) {
    return res.status(404).json({ error: "No account with that email" });
  }

  const resetToken = Math.random().toString(36).substring(2, 15);
  await db.query(
    `UPDATE users SET reset_token = '${resetToken}' WHERE email = '${email}'`
  );

  // En producción enviaríamos un email
  res.json({ message: "Reset email sent", debug_token: resetToken });
}

async function changePassword(req, res) {
  const { token, newPassword } = req.body;

  const result = await db.query(
    `SELECT id FROM users WHERE reset_token = '${token}'`
  );

  if (result.rows.length === 0) {
    return res.status(400).json({ error: "Invalid token" });
  }

  const hashedPassword = await bcrypt.hash(newPassword, 8);
  await db.query(
    `UPDATE users SET password = '${hashedPassword}', reset_token = NULL WHERE id = ${result.rows[0].id}`
  );

  res.json({ message: "Password changed successfully" });
}

module.exports = { register, login, resetPassword, changePassword };
```

```javascript
// auth/middleware.js
const jwt = require("jsonwebtoken");
const JWT_SECRET = "mi-secreto-super-seguro-2024";

function authenticate(req, res, next) {
  const token = req.headers.authorization?.split(" ")[1];
  if (!token) {
    return res.status(401).json({ error: "No token provided" });
  }

  try {
    const decoded = jwt.verify(token, JWT_SECRET);
    req.user = decoded;
    next();
  } catch (error) {
    res.status(401).json({ error: "Invalid token" });
  }
}

function requireAdmin(req, res, next) {
  if (req.user.role !== "admin") {
    return res.status(403).json({ error: "Admin access required" });
  }
  next();
}

module.exports = { authenticate, requireAdmin };
```

---

## Parte 1: Review con el Agente (15 min)

Pide al agente que haga un security review profundo:

```text
Realiza un security review del módulo de autenticación que te proporciono.
No uses un checklist genérico. Busca específicamente:

1. Inputs no validados que llegan a queries, templates o comandos
2. Secrets en código, logs o respuestas HTTP
3. Race conditions en operaciones que modifican datos
4. Authorization bypass (¿puede user A acceder a datos de user B?)
5. Dependencias o APIs usadas de forma insegura

Para cada hallazgo proporciona:
- Severidad: Crítica / Alta / Media / Baja
- Archivo y línea exacta
- Descripción del exploit potencial
- Mitigación con código de ejemplo
```

---

## Parte 2: Verificar los Hallazgos (10 min)

Para cada vulnerabilidad que el agente encuentre, verifica:

1. ¿Es una vulnerabilidad real o un falso positivo?
2. ¿Podría un atacante explotarla sin conocer el código fuente?
3. ¿Cuál sería el impacto concreto si se explota?

### Pistas (no leas hasta haber intentado el review)

El módulo contiene al menos estas vulnerabilidades. Verifica que tu review las encontró todas:

<details>
<summary>Click para ver las pistas</summary>

1. SQL Injection en múltiples queries (concatenación de strings)
2. JWT secret hardcoded en código fuente
3. Token de reset expuesto en la respuesta HTTP (`debug_token`)
4. Token de reset predecible (`Math.random()` no es criptográficamente seguro)
5. Log del token JWT en console.log (fuga de información)
6. Salt rounds de bcrypt demasiado bajos (8 en vez de 12+)
7. Enumeración de usuarios (mensajes de error distintos para "user not found" vs "invalid password")
8. JWT expiry de 30 días sin refresh token
9. No hay rate limiting en login ni en resetPassword
10. No hay validación de complejidad de contraseña

</details>

---

## Parte 3: Implementar Fixes (15 min)

Pide al agente que corrija las 3 vulnerabilidades más críticas:

```text
De las vulnerabilidades encontradas, corrige las 3 más críticas.
Para cada fix:
1. Muestra el código corregido
2. Explica por qué el fix es seguro
3. Incluye un test que verifique que la vulnerabilidad ya no existe
```

---

## Entregable

1. Lista de todas las vulnerabilidades encontradas (mínimo 3, objetivo 6+)
2. Para cada una: severidad, exploit, mitigación
3. Código corregido para las 3 más críticas
4. Reflexión: ¿Cuántas encontraste tú antes de usar el agente? ¿Cuántas encontró el agente que tú no viste?

---

## Criterios de Evaluación

- Se encontraron al menos 3 vulnerabilidades críticas o altas
- Cada vulnerabilidad tiene exploit potencial descrito (no solo "es inseguro")
- Los fixes son correctos y no introducen nuevas vulnerabilidades
- La reflexión demuestra aprendizaje sobre security review asistido por IA
