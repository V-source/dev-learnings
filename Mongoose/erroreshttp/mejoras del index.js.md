El principal objetivo de las mejoras será:

1.  **Modularizar el código**: Separar las configuraciones en archivos dedicados.
2.  **Organizar la estructura**: Agrupar las rutas y la lógica de inicio del servidor en un orden más lógico.
3.  **Mantener la consistencia**: Usar las mismas convenciones para importar y exportar módulos.
4.  **Limpiar la lógica**: Mover la lógica de Socket.io y la ruta `/message` a sus propios módulos.

Aquí te muestro cómo podrías reestructurar y mejorar tu código.

-----

### Versión Mejorada del Código

```javascript
// En tu archivo principal, app.js
import 'dotenv/config';
import express from 'express';
import { createServer } from 'node:http';
import { Server } from 'socket.io';
import setupMiddlewares from './middlewares/index.js'; // Importa el nuevo archivo de middlewares
import connectDB from './db/connection.js'; // Importa la función de conexión a la DB
import setupSocket from './services/socket/index.js'; // Importa el nuevo archivo de Socket.io
import apiRouter from './routes/api.js'; // Importa el router principal
import errorHandler from './middlewares/errorHandler.js'; // Importa el middleware de errores
import ErrorFactory from './utils/errorHandlers/factory.js';

const app = express();
const PORT = process.env.PORT || 5000;

// 1. Conexión a la base de datos
connectDB();

// 2. Configuración de middlewares globales
setupMiddlewares(app);

// 3. Rutas de la API
app.use('/api', apiRouter);

// 4. Manejo de rutas no encontradas (404)
app.use((req, res, next) => {
  console.log(`Ruta no encontrada: ${req.originalUrl}`);
  next(new ErrorFactory(`La ruta ${req.originalUrl} no existe.`, null, 404));
});

// 5. Middleware de manejo de errores global (debe ir al final)
app.use(errorHandler);

// 6. Iniciar el servidor HTTP y Socket.io
const server = createServer(app);
setupSocket(server); // Pasa el servidor HTTP para que Socket.io lo use

server.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
  console.log(`Socket.io available on http://localhost:${PORT}`);
});
```

### Archivos Nuevos y Mejorados

#### 1\. Middleware Global (`middlewares/index.js`)

Aquí centralizas todos tus middlewares de Express.

```javascript
// En un archivo llamado: middlewares/index.js
import cors from 'cors';
import helmet from 'helmet';
import morgan from 'morgan';
import fs from 'node:fs';
import { join, dirname } from 'node:path';
import { fileURLToPath } from 'node:url';
import express from 'express';

const __dirname = dirname(fileURLToPath(import.meta.url));
const logStream = fs.createWriteStream(join(__dirname, '../../access.log'), { flags: 'a' });

const setupMiddlewares = (app) => {
  app.disable('x-powered-by');
  app.use(cors());
  app.use(helmet());
  app.use(express.json());
  app.use(express.urlencoded({ extended: true }));
  app.use(morgan('combined', { stream: logStream }));
};

export default setupMiddlewares;
```

#### 2\. Conexión a la Base de Datos (`db/connection.js`)

Extraer la lógica de conexión a la base de datos hace que tu `app.js` sea más legible.

```javascript
// En un archivo llamado: db/connection.js
import mongoose from 'mongoose';

const connectDB = async () => {
  try {
    await mongoose.connect(process.env.DB_CONNECTION_STRING);
    console.log('Conectado a la base de datos de MongoDB.');
  } catch (error) {
    console.error('No se pudo conectar a la base de datos:', error);
    process.exit(1); // Sale de la aplicación si no se puede conectar
  }
};

export default connectDB;
```

#### 3\. Configuración de Socket.io (`services/socket/index.js`)

Mueve toda la lógica de Socket.io y la ruta `/message` a un solo lugar.

```javascript
// En un archivo llamado: services/socket/index.js
import { Server } from 'socket.io';

const setupSocket = (server) => {
  const io = new Server(server, {
    cors: {
      origin: "*", // Cambia esto a tu dominio en producción
    },
    connectionStateRecovery: {
      maxDisconnectionDuration: 2 * 60 * 1000,
      skipMiddlewares: true,
    }
  });

  const clients = {};

  // Lógica de conexión y manejo de eventos (la que ya tienes)
  io.on('connection', (socket) => {
    // ... tu lógica de conexión de socket ...
  });

  // Puedes centralizar la lógica de emisión de mensajes aquí, en vez de tenerla en una ruta.
  // Pero si necesitas una ruta, puedes definirla así:
  const express = require('express');
  const router = express.Router();

  router.get('/message', (req, res) => {
    if (Object.keys(clients).length > 0) {
      io.emit('massive', { 
        msg: 'Este es un mensaje para TODOS los clientes conectados',
        timestamp: new Date().toISOString(),
        totalClients: Object.keys(clients).length
      });
      res.status(200).json({ 
        msg: 'Mensaje enviado a todos los clientes conectados',
        totalClients: Object.keys(clients).length
      });
    } else {
      res.status(404).json({ error: 'No hay clientes conectados' });
    }
  });

  return router; // Devuelve el router de la ruta /message
};

export default setupSocket;
```

Luego, en tu `app.js`, harías: `app.use(setupSocket(server));` para montar el router de mensajes.

#### 4\. Router Principal de la API (`routes/api.js`)

Este archivo unificará todas tus rutas de la API en un solo lugar.

```javascript
// En un archivo llamado: routes/api.js
import { Router } from 'express';
import ticketsRouter from '../services/tickets/getTickets.js';
// import usersRouter from './users.js'; // si tuvieras más routers

const router = Router();

router.use('/tickets', ticketsRouter);
// router.use('/users', usersRouter); // monta otros routers aquí

export default router;
```

### Resumen de las Mejoras

  * **`app.js` se vuelve el orquestador**: Ahora es mucho más fácil de leer, ya que solo muestra la configuración de alto nivel y el flujo de la aplicación.
  * **Modularidad**: Cada archivo se encarga de una sola cosa (conexión a la DB, middlewares, Socket.io, etc.), lo que facilita la depuración y la escalabilidad.
  * **Organización**: Las rutas están agrupadas bajo un único router principal (`api.js`), lo que te permite añadir más rutas fácilmente.

Este enfoque te proporciona una base de código más limpia y mantenible a medida que tu proyecto crezca.


[Volver al Indice](/home/villegas/git/dev-learnings/)
