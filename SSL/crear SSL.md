El proceso para generar un certificado SSL directamente en tu PC, especialmente para un servidor de desarrollo como Express en Arch Linux, generalmente se realiza con **OpenSSL**. No es recomendable usar un certificado autogenerado en un entorno de producción, pero es perfecto para pruebas locales.

Aquí tienes los pasos para generar un certificado SSL y configurar tu servidor Express para que lo use.

### 1\. Generar la Clave Privada y el Certificado SSL

Primero, necesitas crear un par de archivos: una **clave privada** (`.key`) y un **certificado autofirmado** (`.crt`). Ambos se generan con OpenSSL.

1.  Abre una terminal en Arch Linux.

2.  Ejecuta el siguiente comando para generar la clave privada (`server.key`):

    ```bash
    openssl genrsa -out server.key 2048
    ```

    Este comando crea un archivo `server.key` con una clave privada de 2048 bits.

3.  Ahora, usa el siguiente comando para crear el certificado autofirmado (`server.crt`) a partir de la clave privada. Este comando te pedirá que ingreses información (país, estado, etc.). Puedes dejar la mayoría de los campos en blanco, excepto el **Common Name**, donde puedes poner `localhost` o la dirección IP de tu máquina (`127.0.0.1`).

    ```bash
    openssl req -new -x509 -sha256 -key server.key -out server.crt -days 365
    ```

      - `-new -x509`: Crea un nuevo certificado autofirmado.
      - `-sha256`: Usa un algoritmo de cifrado seguro.
      - `-key server.key`: Usa la clave privada que acabas de generar.
      - `-out server.crt`: Nombre del archivo de salida para el certificado.
      - `-days 365`: El certificado será válido por un año.

### 2\. Configurar el Servidor Express con HTTPS

Ahora que tienes los archivos `server.key` y `server.crt`, necesitas modificar tu código de Express para usar el módulo `https` en lugar de `http`.

A continuación, se muestra cómo adaptar tu código:

```javascript
import "dotenv/config";
import cors from "cors";
import helmet from "helmet";
import morgan from "morgan";
import fs from "node:fs";
import https from "node:https"; // <-- Importa el módulo 'https'
import { join, dirname } from "node:path";
import { fileURLToPath } from "node:url";
import express from "express";

const app = express();
const __dirname = dirname(fileURLToPath(import.meta.url));
const PORT = process.env.PORT || 5000;
const logStream = fs.createWriteStream(join(__dirname, "../access.log"), {
  flags: "a",
});

// Configuración de CORS para Express
app.disable("x-powered-by");
app.use(cors());
app.use(helmet());
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
app.use(morgan("combined", { stream: logStream }));

// ── DB CONNECTION ───────────────────────────────────────────────────
import "./db/connection.js";

import errorHandler from "./middlewares/errorHandler.js";
import ErrorFactory from "./utils/errorHandlers/factory.js";
import notificationToken from "./services/notifications/storeNotificationToeken.service.js";
import masiveNotification from "./services/notifications/masiveNotifications.service.js";
import apkLogs from "./services/apkLogs.service.js";

app.get("/", (req, res) => {
  res.status(200).json({ msg: "express server" });
});

app.use("/api", notificationToken);
app.use("/api", masiveNotification);
app.use("/api", apkLogs);

// Manejo de rutas no encontradas (404)
app.use((req, res, next) => {
  console.log(`Ruta no encontrada: ${req.originalUrl}`);
  next(new ErrorFactory(`La ruta ${req.originalUrl} no existe.`, null, 404));
});

// Middleware de manejo de errores global (debe ir al final)
app.use(errorHandler);

// ── Configuración HTTPS ───────────────────────────────────────────

const privateKey = fs.readFileSync(join(__dirname, 'server.key'), 'utf8');
const certificate = fs.readFileSync(join(__dirname, 'server.crt'), 'utf8');

const credentials = { key: privateKey, cert: certificate };

// Crea el servidor HTTPS
const httpsServer = https.createServer(credentials, app);

httpsServer.listen(PORT, () => {
  console.log(`Server running on port ${PORT} with HTTPS`);
});

```

### Resumen de los Cambios en el Código

1.  **Importar `https`**: Se importa el módulo `https` de Node.js.
2.  **Leer los Archivos**: Se leen los archivos `server.key` y `server.crt` que generaste en el primer paso. Asegúrate de que estos archivos estén en la misma carpeta que tu archivo de servidor.
3.  **Crear el Servidor HTTPS**: Se usa `https.createServer()` para crear el servidor, pasando un objeto de credenciales con la clave y el certificado, y la instancia de tu aplicación Express (`app`).
4.  **Iniciar el Servidor HTTPS**: Se llama a `httpsServer.listen()` en lugar de `app.listen()` para que el servidor escuche las solicitudes seguras.

Al ejecutar tu servidor modificado, ahora estará sirviendo en HTTPS. Cuando intentes acceder a él en tu navegador (`https://localhost:5000`), el navegador te advertirá que el certificado no es de confianza. Esto es normal, ya que es autofirmado, pero puedes ignorar la advertencia y continuar.
