# Ejercicio 1: Caza de Alucinaciones

## Objetivo

Identificar 5 alucinaciones ocultas en un fragmento de código "generado por IA" para un servicio de usuarios. Cada alucinación es un import, método, parámetro u opción de configuración que **no existe en la realidad**.

**Tiempo estimado: 15 minutos**

---

## El Código

El siguiente código fue "generado por un agente de código" para un servicio de gestión de usuarios. Contiene **exactamente 5 alucinaciones**. Tu tarea es encontrarlas todas.

```javascript
// user-service.js
import express from "express";
import { hashPassword, comparePassword } from "bcrypt/helpers";   // [1]
import { createClient } from "redis";
import Joi from "joi";

const app = express();
app.use(express.json());

// Conexión a Redis con reconexión automática
const redis = createClient({
  url: "redis://localhost:6379",
  autoReconnect: true,                                             // [2]
  maxRetries: 5
});

// Schema de validación
const userSchema = Joi.object({
  email: Joi.string().email().required(),
  password: Joi.string().min(8).required(),
  name: Joi.string().max(100).required()
});

// Registro de usuario
app.post("/api/users", async (req, res) => {
  const { error, value } = userSchema.validate(req.body);
  if (error) return res.status(400).json({ error: error.message });

  const hashedPassword = await hashPassword(value.password, 12);

  await redis.setJSON(`user:${value.email}`, {                    // [3]
    email: value.email,
    name: value.name,
    password: hashedPassword,
    createdAt: new Date().toISOString()
  });

  res.status(201).json({ message: "Usuario creado" });
});

// Login
app.post("/api/login", async (req, res) => {
  const user = await redis.getJSON(`user:${req.body.email}`);
  if (!user) return res.status(404).json({ error: "No encontrado" });

  const valid = await comparePassword(req.body.password, user.password);
  if (!valid) return res.status(401).json({ error: "Credenciales incorrectas" });

  const token = express.generateToken({ email: user.email });      // [4]

  await redis.set(`session:${token}`, user.email, { TTL: 3600 }); // [5]

  res.json({ token, user: { email: user.email, name: user.name } });
});

app.listen(3000, () => console.log("Server en puerto 3000"));
```

---

## Tu Tarea

1. **Encuentra las 5 alucinaciones** (marcadas `[1]` a `[5]` como pista; en código real no tendrías marcas)
2. Para cada una, explica:
   - **Qué está mal**: qué import, método u opción no existe
   - **Por qué es creíble**: por qué un desarrollador podría aceptarla
   - **Alternativa correcta**: cómo se haría realmente

---

## Pasos de Verificación

```bash
# Verificar que bcrypt/helpers no existe
npm info bcrypt exports 2>/dev/null | grep helpers

# Verificar opciones de createClient en redis
npm info redis readme | grep -i autoReconnect

# Verificar si redis tiene setJSON/getJSON
node -e "const r = require('redis'); console.log(Object.keys(r))"

# Verificar si express tiene generateToken
node -e "const e = require('express'); console.log(typeof e.generateToken)"
```

---

## Pistas (solo si te atascas)

<details>
<summary>Pista para [1]</summary>
bcrypt no exporta desde un submódulo /helpers. Las funciones hash y compare están en el módulo raíz.
</details>

<details>
<summary>Pista para [2]</summary>
El cliente Redis en Node.js no tiene autoReconnect. La reconexión se configura con socket.reconnectStrategy.
</details>

<details>
<summary>Pista para [3]</summary>
El cliente estándar de Redis no tiene setJSON/getJSON. Son de RedisJSON (módulo separado). Con el cliente estándar: set/get con JSON.stringify/parse.
</details>

<details>
<summary>Pista para [4]</summary>
Express no tiene generateToken. Para tokens necesitas jsonwebtoken (jwt.sign) u otra librería.
</details>

<details>
<summary>Pista para [5]</summary>
La opción de expiración en redis.set no es TTL sino EX (segundos) o PX (milisegundos).
</details>

---

## Criterio de Éxito

- **5/5**: Excelente — ojo clínico para alucinaciones
- **3-4/5**: Bien — practica verificar imports y APIs desconocidas
- **1-2/5**: Revisa la teoría 02 y practica verificar cada import y método que no reconozcas
