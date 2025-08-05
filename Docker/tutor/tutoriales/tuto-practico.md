¡Perfecto! Vamos a crear un proyecto **fullstack simple** con:

* ✅ Backend en **Node.js + Express**
* ✅ Base de datos **MongoDB**
* ✅ Frontend con **Vite + React**
* ✅ Comunicación vía **Docker Compose**
* ✅ Una única pantalla con 2 botones que hacen peticiones al backend

> 📚 Está diseñado para ser **clarísimo, simple y funcional**. Ideal para entender cómo se integran todos los componentes en Docker.

---

# ✅ CHECKLIST GENERAL

| Etapa | Tarea                                                      | Estado |
| ----- | ---------------------------------------------------------- | ------ |
| 📁 1  | Crear estructura de carpetas del proyecto                  | 🔲     |
| 🛠️ 2 | Configurar el backend con Express y conexión a MongoDB     | 🔲     |
| 📦 3  | Crear el Dockerfile del backend                            | 🔲     |
| 🧪 4  | Crear el esquema de usuario con Mongoose y 2 rutas         | 🔲     |
| 💻 5  | Crear frontend en Vite con 2 botones y consumo del backend | 🔲     |
| 📦 6  | Crear el Dockerfile del frontend                           | 🔲     |
| 🧩 7  | Crear archivo `docker-compose.yml`                         | 🔲     |
| ▶️ 8  | Probar toda la aplicación funcionando                      | 🔲     |

---

# 📁 ESTRUCTURA DEL PROYECTO

```
fullstack-app/
├── backend/
│   ├── Dockerfile
│   ├── package.json
│   ├── src/
│   │   ├── index.js
│   │   └── models/User.js
├── frontend/
│   ├── Dockerfile
│   └── (proyecto Vite aquí)
├── docker-compose.yml
```

---

# 🧠 PASO 1: INICIAR EL BACKEND (Express + Mongoose)

### `backend/package.json`

```json
{
  "name": "simple-api",
  "version": "1.0.0",
  "main": "src/index.js",
  "scripts": {
    "start": "node src/index.js"
  },
  "dependencies": {
    "cors": "^2.8.5",
    "express": "^4.18.2",
    "mongoose": "^7.3.4"
  }
}
```

```bash
cd backend
npm install
```

---

### `backend/src/models/User.js`

```js
const mongoose = require("mongoose");

const UserSchema = new mongoose.Schema({
  nombre: String,
  rol: String
});

module.exports = mongoose.model("User", UserSchema);
```

---

### `backend/src/index.js`

```js
const express = require("express");
const mongoose = require("mongoose");
const cors = require("cors");
const User = require("./models/User");

const app = express();
app.use(cors());
app.use(express.json());

mongoose.connect("mongodb://mongo:27017/test", {
  useNewUrlParser: true,
  useUnifiedTopology: true
});

// Ruta 1: Crear usuario
app.post("/crear", async (req, res) => {
  const { nombre, rol } = req.body;
  const user = await User.create({ nombre, rol });
  res.json(user);
});

// Ruta 2: Obtener todos los usuarios
app.get("/usuarios", async (req, res) => {
  const usuarios = await User.find();
  res.json(usuarios);
});

app.listen(3000, () => {
  console.log("API corriendo en http://localhost:3000");
});
```

---

### `backend/Dockerfile`

```Dockerfile
FROM node:18

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

---

# 🧠 PASO 2: INICIAR EL FRONTEND (Vite + React)

```bash
cd ..
npm create vite@latest frontend -- --template react
cd frontend
npm install
```

### 🧪 Código del frontend (solo 1 pantalla)

Edita `frontend/src/App.jsx`:

```jsx
import { useState } from 'react';

function App() {
  const [usuarios, setUsuarios] = useState([]);

  const crearUsuario = async () => {
    const res = await fetch('http://localhost:3000/crear', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ nombre: "Juan", rol: "Admin" })
    });
    const data = await res.json();
    console.log("Usuario creado:", data);
  };

  const obtenerUsuarios = async () => {
    const res = await fetch('http://localhost:3000/usuarios');
    const data = await res.json();
    setUsuarios(data);
  };

  return (
    <div style={{ padding: 20 }}>
      <h1>Usuarios</h1>
      <button onClick={crearUsuario}>Crear Usuario</button>
      <button onClick={obtenerUsuarios}>Ver Usuarios</button>
      <ul>
        {usuarios.map((u, i) => (
          <li key={i}>{u.nombre} - {u.rol}</li>
        ))}
      </ul>
    </div>
  );
}

export default App;
```

---

### `frontend/Dockerfile`

```Dockerfile
FROM node:18

WORKDIR /app

COPY . .
RUN npm install
RUN npm run build

RUN npm install -g serve
EXPOSE 4173

CMD ["serve", "-s", "dist"]
```

---

# 🧩 PASO 3: DOCKER COMPOSE

### `docker-compose.yml`

```yaml
version: '3.8'

services:
  mongo:
    image: mongo
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db

  backend:
    build: ./backend
    ports:
      - "3000:3000"
    depends_on:
      - mongo

  frontend:
    build: ./frontend
    ports:
      - "5173:4173"
    depends_on:
      - backend

volumes:
  mongo_data:
```

---

# ▶️ PASO FINAL: Ejecutar todo

Desde la raíz del proyecto:

```bash
docker-compose up --build
```

* Ve a `http://localhost:5173` y prueba los botones.
* Verás los usuarios guardados en MongoDB.

---

# 🧾 RESUMEN TÉCNICO

| Componente    | Tecnología                   | Puerto |
| ------------- | ---------------------------- | ------ |
| Frontend      | React + Vite                 | 5173   |
| Backend       | Node.js + Express + Mongoose | 3000   |
| Base de datos | MongoDB                      | 27017  |

---

¿Deseas que prepare este proyecto como `.zip` o un `repo Git` con instrucciones para compartir? ¿Te gustaría agregar autenticación o validaciones más adelante?

---



# ¿ES NECESARIO UN DOCKERFILE  DE BASE DE DATOS PARA?

Excelente pregunta.
**No, no es necesario crear un `Dockerfile` para la base de datos si estás usando una imagen oficial** como `mongo`.

---


### 🧠 ¿Por qué?

Cuando usas este bloque en `docker-compose.yml`:

```yaml
mongo:
  image: mongo
  ports:
    - "27017:27017"
  volumes:
    - mongo_data:/data/db
```

Estás diciendo:

> “Usa la imagen oficial de MongoDB desde Docker Hub, no necesito personalizarla.”

Docker se encargará de:

* Descargar esa imagen.
* Crear el contenedor listo para usar.
* Exponer el puerto.
* Montar el volumen persistente.

---

### 🧱 ¿Cuándo sí necesitas un Dockerfile para una base de datos?

Solo si quieres **personalizarla**:

* Agregar scripts de inicio (por ejemplo, para crear usuarios o bases de datos automáticamente).
* Cambiar configuración interna del servidor de base de datos.
* Incluir herramientas extra.
* Hacer una imagen con tus propios datos pre-cargados.

---

### 🧪 Si quisieras hacerlo, sería así:

#### `mongo/Dockerfile` (ejemplo opcional)

```Dockerfile
FROM mongo

COPY init.js /docker-entrypoint-initdb.d/
```

Y `init.js` podría contener:

```js
db = db.getSiblingDB('mydb');
db.createUser({
  user: "admin",
  pwd: "secret",
  roles: [{ role: "readWrite", db: "mydb" }]
});
```

---

### ✅ Conclusión:

Para proyectos simples (como el que estás haciendo), **no necesitas crear un Dockerfile para la base de datos**, solo usa imágenes oficiales.

Pero si más adelante necesitas mayor control o inicialización automática de datos/usuarios, **sí podrías crear uno**.

¿Quieres que te muestre cómo agregar usuarios y datos de ejemplo con un archivo de inicialización?


---


¡Perfecto! Esto es **fundamental** para proyectos reales. Vamos a adaptar el ejemplo para tener dos entornos bien diferenciados:

---

# 🏗️ ¿Por qué separar "desarrollo" y "producción"?

| Entorno       | Características principales                                                                                                 |
| ------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 🧪 Desarrollo | - Recarga en caliente (hot reload) <br> - Acceso fácil a logs y herramientas <br> - Código editable en tiempo real          |
| 🚀 Producción | - Código compilado y optimizado <br> - Seguridad y rendimiento <br> - Contenedores estables, sin herramientas de desarrollo |

---

## ✅ ¿Qué cambia entre ambos?

* **Archivos `Dockerfile` diferentes** (dev y prod)
* **Modo de ejecución diferente en Compose**
* **Vite en modo dev o modo build**
* **Variables de entorno**
* Volúmenes en desarrollo para mantener el código sincronizado

---

# 🧾 ESTRUCTURA ACTUALIZADA

```
fullstack-app/
├── backend/
│   ├── Dockerfile.dev
│   ├── Dockerfile
│   ├── package.json
│   ├── src/
├── frontend/
│   ├── Dockerfile.dev
│   ├── Dockerfile
│   ├── vite.config.js
│   ├── src/
├── docker-compose.dev.yml
├── docker-compose.prod.yml
```

---

# ⚙️ DOCKERFILEs PARA CADA ENTORNO

---

## ✅ BACKEND

### `backend/Dockerfile.dev`

```Dockerfile
FROM node:18

WORKDIR /app
COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "run", "dev"]
```

> 🔁 Suponemos que agregas `nodemon` como devDependency y script `"dev": "nodemon src/index.js"`

---

### `backend/Dockerfile` (producción)

```Dockerfile
FROM node:18

WORKDIR /app
COPY package*.json ./
RUN npm install --omit=dev

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

---

## ✅ FRONTEND

### `frontend/Dockerfile.dev`

```Dockerfile
FROM node:18

WORKDIR /app
COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 5173

CMD ["npm", "run", "dev", "--", "--host"]
```

---

### `frontend/Dockerfile` (producción)

```Dockerfile
FROM node:18 as builder

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Etapa de producción
FROM node:18

WORKDIR /app
RUN npm install -g serve
COPY --from=builder /app/dist ./dist

EXPOSE 4173

CMD ["serve", "-s", "dist"]
```

---

# 🔧 DOCKER COMPOSE PARA CADA ENTORNO

---

## 🧪 `docker-compose.dev.yml`

```yaml
version: '3.8'

services:
  mongo:
    image: mongo
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile.dev
    ports:
      - "3000:3000"
    volumes:
      - ./backend:/app
    depends_on:
      - mongo

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile.dev
    ports:
      - "5173:5173"
    volumes:
      - ./frontend:/app
    depends_on:
      - backend

volumes:
  mongo_data:
```

📌 Aquí usamos `volumes` para **mantener el código sincronizado** con el contenedor (hot reload).

---

## 🚀 `docker-compose.prod.yml`

```yaml
version: '3.8'

services:
  mongo:
    image: mongo
    restart: always
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    depends_on:
      - mongo

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    ports:
      - "4173:4173"
    depends_on:
      - backend

volumes:
  mongo_data:
```

📌 Aquí todo está compilado y optimizado. Sin montajes de archivos, sin nodemon ni Vite dev server.

---

# ▶️ ¿Cómo se ejecutan?

### 🧪 Desarrollo:

```bash
docker-compose -f docker-compose.dev.yml up --build
```

### 🚀 Producción:

```bash
docker-compose -f docker-compose.prod.yml up --build
```

---

# 💡 Tips adicionales

* Puedes usar `.env.dev` y `.env.prod` para manejar variables.
* Para ambientes de producción reales, se recomienda también:

  * Agregar proxy inverso (como Nginx o Traefik)
  * SSL (Let's Encrypt)
  * Monitorización (Prometheus, Grafana)
  * Logs centralizados
  * Docker Secrets (para contraseñas)

---

# 🧠 En resumen

| Recurso             | Desarrollo               | Producción                |
| ------------------- | ------------------------ | ------------------------- |
| Backend Dockerfile  | `Dockerfile.dev`         | `Dockerfile`              |
| Frontend Dockerfile | `Dockerfile.dev`         | `Dockerfile`              |
| Compose file        | `docker-compose.dev.yml` | `docker-compose.prod.yml` |
| Código editable     | Sí (con volumen)         | No (imagen cerrada)       |
| Optimización        | No                       | Sí                        |

---

¿Quieres que empaquete este proyecto como base descargable?
¿O te gustaría que ahora agreguemos autenticación o conexión a una base externa para pruebas reales?

---

# 🧩 PASO 4: AGREGANDO DATOS DE EJEMPLO

Perfecto, ahora te mostraré cómo **pre-cargar usuarios y datos de ejemplo en MongoDB** automáticamente al levantar tu contenedor.

---

## 🎯 ¿Qué vamos a hacer?

Cuando usas la imagen oficial de MongoDB, puedes **inyectar scripts de inicialización** en JavaScript (o JSON) ubicándolos en:

```
/docker-entrypoint-initdb.d/
```

Mongo ejecutará **automáticamente** cualquier archivo `.js`, `.sh` o `.json` dentro de esa carpeta al iniciar la base de datos por primera vez (es decir, cuando el volumen está vacío).

---

## ✅ Objetivo

Crear automáticamente:

* una base de datos: `test`
* una colección: `users`
* con documentos como:

```json
{ "nombre": "Alice", "rol": "Gerente" }
{ "nombre": "Bob", "rol": "Técnico" }
```

---

## 📁 Estructura del proyecto con init script

```
fullstack-app/
├── backend/
├── frontend/
├── mongo-init/
│   └── init.js
├── docker-compose.dev.yml
```

---

## 🧾 `mongo-init/init.js`

```js
db = db.getSiblingDB('test'); // nombre de la base de datos

db.createCollection('users');

db.users.insertMany([
  { nombre: "Alice", rol: "Gerente" },
  { nombre: "Bob", rol: "Técnico" }
]);
```

> 📌 Este archivo será ejecutado solo una vez, **al inicializar la base**.

---

## ✏️ Modifica `docker-compose.dev.yml` (o prod si aplica)

Agrega el montaje de volumen al servicio `mongo`:

```yaml
services:
  mongo:
    image: mongo
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db
      - ./mongo-init:/docker-entrypoint-initdb.d
```

> 🔁 Recuerda: si `mongo_data` ya existe (ya tiene datos), el script **NO se ejecutará de nuevo**. Tendrías que borrar el volumen.

---

## 💥 Ejecutar desde cero

Para que funcione correctamente, debes **eliminar los volúmenes** actuales para que Mongo vuelva a inicializarse:

```bash
docker-compose -f docker-compose.dev.yml down -v
docker-compose -f docker-compose.dev.yml up --build
```

---

## ✅ Verifica los datos insertados

Puedes probar con una llamada HTTP a tu backend (si lo conectas a `/usuarios`) o usar Mongo Compass / MongoDB Shell para ver los datos:

```bash
docker exec -it fullstack-app-mongo-1 mongosh

use test
db.users.find()
```

---

## 🧠 ¿Qué más puedes hacer desde este script?

* Crear usuarios de base de datos con roles.
* Crear índices.
* Inicializar múltiples colecciones.
* Usar condicionales (`if` para evitar duplicados).
* Conectarte a un clúster remoto (con más configuración).

---

¿Quieres que prepare una versión que también cree un usuario y contraseña para MongoDB con roles?
¿O prefieres ahora montar una base remota (Mongo Atlas, por ejemplo) como producción externa?


---

# 🧩 PASO 5: USUARIO Y CONTRASE;A PARA MONGODB - USO DE VARIABLES DE ENTORNO 

Perfecto. Te enseñaré cómo:

---

## 🎯 Objetivo

* Crear una **base de datos llamada `test`**
* Crear un **usuario con nombre y contraseña**
* Darle el rol `readWrite` en esa base
* Y cargar algunos datos de ejemplo automáticamente

---

## 🧾 1. Estructura del proyecto

```
fullstack-app/
├── mongo-init/
│   └── init.js
├── docker-compose.dev.yml
```

---

## 🔐 2. Variables de entorno en `docker-compose.dev.yml`

```yaml
services:
  mongo:
    image: mongo
    ports:
      - "27017:27017"
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: secret123
      MONGO_INITDB_DATABASE: test
    volumes:
      - mongo_data:/data/db
      - ./mongo-init:/docker-entrypoint-initdb.d

volumes:
  mongo_data:
```

> 📌 Estos valores se usan en el arranque para crear el **usuario root del sistema de MongoDB** y apuntar el init a una base inicial.

---

## 📄 3. Archivo `mongo-init/init.js`

```js
// Seleccionar la base donde se crearán usuarios y datos
db = db.getSiblingDB('test');

// Crear usuario con rol
db.createUser({
  user: "appuser",
  pwd: "apppassword123",
  roles: [{ role: "readWrite", db: "test" }]
});

// Crear colección y agregar datos de ejemplo
db.createCollection('users');

db.users.insertMany([
  { nombre: "Alice", rol: "Gerente" },
  { nombre: "Bob", rol: "Técnico" }
]);
```

---

## 🧪 4. ¿Cómo probarlo?

Primero borra los volúmenes para forzar inicialización:

```bash
docker-compose -f docker-compose.dev.yml down -v
docker-compose -f docker-compose.dev.yml up --build
```

Después entra al contenedor y conéctate con las credenciales:

```bash
docker exec -it fullstack-app-mongo-1 mongosh -u appuser -p apppassword123 --authenticationDatabase test
```

Luego en el shell de Mongo:

```js
use test
db.users.find()
```

---

## 🛡️ 5. Seguridad y buenas prácticas

| Consejo                                                  | Detalle                                           |
| -------------------------------------------------------- | ------------------------------------------------- |
| 🔐 Usa `.env`                                            | No pongas claves directamente en `docker-compose` |
| 🔄 Elimina el volumen si necesitas reinicializar la base | `docker-compose down -v`                          |
| 📦 Usa `init.js` solo para inicialización                | No para lógica compleja ni backups                |

---

## 🧠 Recapitulación

| Acción                        | Resultado                                              |
| ----------------------------- | ------------------------------------------------------ |
| `MONGO_INITDB_ROOT_*`         | Crea un usuario root admin                             |
| `init.js` ejecutado al inicio | Se crea base `test`, usuario appuser y se cargan datos |
| Datos persistentes            | Gracias al volumen `mongo_data`                        |
| Autenticación requerida       | Acceso solo si usas el usuario creado                  |

---

¿Quieres que ahora prepare una conexión segura desde el backend a esta base con usuario y contraseña? ¿O prefieres agregar autenticación JWT al backend primero?


---

ambas estaria bien:

¡Perfecto! Vamos paso a paso.

---

# ✅ Parte 1: Conexión del backend a MongoDB **con usuario y contraseña**

---

## 📁 1. Archivos involucrados en el backend

```
backend/
├── .env
├── src/
│   └── index.js
├── Dockerfile.dev
├── package.json
```

---

## 🧾 2. Contenido del `.env`

Este archivo contiene las credenciales y URL de conexión:

```env
MONGO_USER=appuser
MONGO_PASS=apppassword123
MONGO_DB=test
MONGO_HOST=mongo
MONGO_PORT=27017
PORT=3000
JWT_SECRET=supersecretkey
```

> ⚠️ No olvides agregar `.env` a tu `.dockerignore`

---

## 🔌 3. Backend – Conexión en `index.js`

```js
import express from 'express';
import mongoose from 'mongoose';
import dotenv from 'dotenv';
import cors from 'cors';

dotenv.config();

const app = express();
app.use(express.json());
app.use(cors());

const mongoURI = `mongodb://${process.env.MONGO_USER}:${process.env.MONGO_PASS}@${process.env.MONGO_HOST}:${process.env.MONGO_PORT}/${process.env.MONGO_DB}?authSource=${process.env.MONGO_DB}`;

mongoose.connect(mongoURI)
  .then(() => console.log("✅ Conectado a MongoDB"))
  .catch(err => console.error("❌ Error conectando a MongoDB:", err));

// Rutas simples
app.get('/ping', (req, res) => {
  res.json({ pong: true });
});

app.get('/usuarios', async (req, res) => {
  const usuarios = await mongoose.connection.db.collection('users').find().toArray();
  res.json(usuarios);
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`🚀 Backend corriendo en puerto ${PORT}`);
});
```

---

## ⚙️ 4. Dockerfile.dev ajustado

Asegúrate de copiar el `.env`:

```Dockerfile
FROM node:18

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
COPY .env .env

EXPOSE 3000
CMD ["npm", "run", "dev"]
```

---

## 🔁 5. Agrega `.env` en volumen de desarrollo o cópialo en producción

En `docker-compose.dev.yml`, no hace falta hacer nada si ya se copia en el `Dockerfile`.

---

# 🛡️ Parte 2: Agregar autenticación JWT al backend

---

## 1️⃣ Instala las dependencias

```bash
npm install jsonwebtoken bcryptjs
```

---

## 2️⃣ Modelo de Usuario simple (`models/User.js`)

```js
import mongoose from 'mongoose';

const userSchema = new mongoose.Schema({
  nombre: String,
  rol: String,
  email: String,
  password: String,
});

export default mongoose.model('User', userSchema);
```

---

## 3️⃣ Rutas de autenticación

### `routes/auth.js`

```js
import express from 'express';
import jwt from 'jsonwebtoken';
import bcrypt from 'bcryptjs';
import User from '../models/User.js';

const router = express.Router();

router.post('/register', async (req, res) => {
  const { nombre, rol, email, password } = req.body;
  const hashed = await bcrypt.hash(password, 10);
  const user = new User({ nombre, rol, email, password: hashed });
  await user.save();
  res.json({ message: 'Usuario registrado' });
});

router.post('/login', async (req, res) => {
  const { email, password } = req.body;
  const user = await User.findOne({ email });

  if (!user) return res.status(401).json({ error: 'Usuario no encontrado' });

  const match = await bcrypt.compare(password, user.password);
  if (!match) return res.status(401).json({ error: 'Contraseña incorrecta' });

  const token = jwt.sign(
    { id: user._id, nombre: user.nombre, rol: user.rol },
    process.env.JWT_SECRET,
    { expiresIn: '1h' }
  );

  res.json({ token });
});

export default router;
```

---

## 4️⃣ Middleware de autenticación

### `middlewares/auth.js`

```js
import jwt from 'jsonwebtoken';

export function verificarToken(req, res, next) {
  const header = req.headers['authorization'];
  if (!header) return res.status(401).json({ error: 'Token requerido' });

  const token = header.split(' ')[1];

  jwt.verify(token, process.env.JWT_SECRET, (err, decoded) => {
    if (err) return res.status(403).json({ error: 'Token inválido' });
    req.user = decoded;
    next();
  });
}
```

---

## 5️⃣ Proteger una ruta (por ejemplo `/privado`)

En tu `index.js`:

```js
import authRoutes from './routes/auth.js';
import { verificarToken } from './middlewares/auth.js';

app.use('/auth', authRoutes);

app.get('/privado', verificarToken, (req, res) => {
  res.json({ message: `Hola ${req.user.nombre}, tienes acceso.` });
});
```

---

## 🧪 Pruebas

1. `POST /auth/register` con:

```json
{
  "nombre": "Alice",
  "rol": "admin",
  "email": "alice@empresa.com",
  "password": "1234"
}
```

2. `POST /auth/login` → te devuelve un `token`

3. `GET /privado` con header:

```
Authorization: Bearer <token>
```

---

## 🧠 Conclusión

✅ Con esto tienes:

* Conexión segura con Mongo usando credenciales
* Autenticación basada en tokens JWT
* Protección de rutas privadas
* Carga de datos iniciales automática con usuarios
* Infraestructura lista para desarrollo o producción

---

¿Te gustaría ahora implementar esta autenticación también en el frontend (Vite) y hacer login/registro visual?
¿O conectarlo a un entorno remoto como Mongo Atlas?
