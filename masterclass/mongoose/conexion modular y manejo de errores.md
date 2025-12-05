La idea será dividirlo en **piezas reutilizables**:

* Configuración de variables de entorno
* Módulo de conexión a la base de datos
* Módulo de logging
* Manejo centralizado de errores

---

## **1️⃣ config/env.js** — Carga de variables de entorno

```js
// config/env.js
import dotenv from "dotenv";
dotenv.config();

if (!process.env.MONGODB_URI) {
  throw new Error("❌ Error: MONGODB_URI no está definido en el archivo .env");
}

export const MONGODB_URI = process.env.MONGODB_URI;
```

---

## **2️⃣ utils/logger.js** — Logger básico con colores

```js
// utils/logger.js
import chalk from "chalk";

export const logger = {
  info: (msg) => console.log(chalk.blue("[INFO]"), msg),
  success: (msg) => console.log(chalk.green("[SUCCESS]"), msg),
  warn: (msg) => console.log(chalk.yellow("[WARN]"), msg),
  error: (msg) => console.error(chalk.red("[ERROR]"), msg),
};
```

---

## **3️⃣ db/connection.js** — Conexión modular a MongoDB

```js
// db/connection.js
import mongoose from "mongoose";
import { logger } from "../utils/logger.js";
import { MONGODB_URI } from "../config/env.js";

mongoose.set("strictQuery", true); // Evita consultas no definidas

export async function connectDB() {
  try {
    const db = await mongoose.connect(MONGODB_URI, {
      serverSelectionTimeoutMS: 30000, // 30 segundos de espera
    });

    logger.success(`Conectado a MongoDB: ${db.connection.name}`);
    logger.info(`Host: ${db.connection.host}`);
    logger.info(`Puerto: ${db.connection.port}`);
  } catch (error) {
    logger.error(`Error al conectar a MongoDB: ${error.message}`);
    process.exit(1); // Detiene el servidor si la DB no se conecta
  }
}
```

---

## **4️⃣ middlewares/errorHandler.js** — Manejo centralizado de errores

```js
// middlewares/errorHandler.js
import { logger } from "../utils/logger.js";

export function errorHandler(err, req, res, next) {
  logger.error(err.stack || err.message);

  res.status(err.status || 500).json({
    status: "error",
    message: err.message || "Error interno del servidor",
  });
}
```

---

## **5️⃣ server.js** — Integración estilo Lego

```js
// server.js
import express from "express";
import { connectDB } from "./db/connection.js";
import { errorHandler } from "./middlewares/errorHandler.js";
import { logger } from "./utils/logger.js";

const app = express();

// Middlewares globales
app.use(express.json());

// Conexión a la base de datos
await connectDB();

// Rutas de ejemplo
app.get("/", (req, res) => {
  res.send("Servidor funcionando 🚀");
});

// Manejo de errores (debe ir al final)
app.use(errorHandler);

// Levantar servidor
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => logger.success(`Servidor corriendo en http://localhost:${PORT}`));
```

---

✅ **Ventajas de esta estructura modular estilo Lego**

* Puedes reemplazar cualquier pieza (logger, conexión, etc.) sin romper el resto.
* Evita que tu servidor arranque sin conexión a la base de datos.
* Manejo de errores centralizado y uniforme.
* Código más legible y fácil de mantener.

---

Si quieres, puedo **añadir un módulo “dbSeeder.js”** que se ejecute solo en entornos de desarrollo para poblar tu base de datos automáticamente cuando se conecte.
Ese módulo encajaría perfecto aquí.

¿Quieres que te lo agregue ya integrado?
