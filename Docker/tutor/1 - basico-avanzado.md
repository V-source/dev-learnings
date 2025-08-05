Perfecto. Vamos a construir una explicación paso a paso y **progresiva** sobre cómo crear contenedores de tres formas distintas:

1. **Usando la CLI (línea de comandos).**
2. **Usando Dockerfile.**
3. **Usando Docker Compose para varios contenedores.**

Todo esto desde cero, con explicaciones claras y ejemplos funcionales para un proyecto realista que incluye:

* Un **backend en Node.js** (API REST).
* Un **frontend en Vite (React)**.
* Una **base de datos**, que puede ser **MongoDB** o **PostgreSQL** (verás ambas).

---

# 🧱 1. CREAR CONTENEDORES USANDO SOLO LA CLI

Este es el método más básico: iniciar contenedores directamente desde imágenes oficiales.

### 🎯 Ejemplo: Ejecutar MongoDB y conectar desde tu PC

```bash
docker run -d \
  --name mongodb \
  -p 27017:27017 \
  -v mongodb_data:/data/db \
  mongo
```

✅ **Explicación:**

| Parte                      | Significado                                          |
| -------------------------- | ---------------------------------------------------- |
| `docker run -d`            | Ejecuta el contenedor en segundo plano.              |
| `--name mongodb`           | Le da un nombre para poder referenciarlo fácilmente. |
| `-p 27017:27017`           | Expone el puerto de MongoDB al host.                 |
| `-v mongodb_data:/data/db` | Usa un volumen persistente para guardar los datos.   |
| `mongo`                    | Es la imagen oficial de MongoDB.                     |

### 🛠 Conectarse desde Node.js

Instala el paquete `mongodb` en tu backend y usa `mongodb://localhost:27017` como URL de conexión.

---

# 🐳 2. CREAR CONTENEDORES USANDO DOCKERFILE

Esto te permite **crear tu propia imagen personalizada**.

---

## 📦 Ejemplo: Backend Node.js con Express

### Paso 1: Estructura de carpetas

```
backend/
├── Dockerfile
├── package.json
├── package-lock.json
└── src/
    └── index.js
```

### Paso 2: Código básico `src/index.js`

```js
const express = require('express');
const app = express();

app.get('/', (req, res) => res.send('API funcionando'));

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Servidor escuchando en puerto ${PORT}`));
```

### Paso 3: `package.json`

```json
{
  "name": "api-ejemplo",
  "version": "1.0.0",
  "main": "src/index.js",
  "scripts": {
    "start": "node src/index.js"
  },
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

### Paso 4: `Dockerfile`

```Dockerfile
# Imagen base
FROM node:18

# Directorio de trabajo en el contenedor
WORKDIR /app

# Copiar archivos del proyecto
COPY package*.json ./
RUN npm install

# Copiar el resto del proyecto
COPY . .

# Puerto expuesto
EXPOSE 3000

# Comando para iniciar la app
CMD ["npm", "start"]
```

### Paso 5: Construir y correr

```bash
cd backend
docker build -t mi-backend .
docker run -d -p 3000:3000 --name backend mi-backend
```

✅ Ahora puedes ir a `http://localhost:3000` y ver tu API.

---

# 📦 3. CREAR ENTORNOS COMPLETOS CON DOCKER COMPOSE

Ideal para manejar **múltiples contenedores** (frontend, backend y base de datos).

---

## 🧪 Caso 1: Backend + MongoDB

### 📁 Estructura:

```
backend/
│
├── Dockerfile
├── package.json
├── src/index.js
├── .dockerignore
└── docker-compose.yml
```

### 📝 `.dockerignore`

```
node_modules
npm-debug.log
```

### 📝 `docker-compose.yml` (backend + MongoDB)

```yaml
version: '3.8'
services:
  backend:
    build: .
    container_name: api_node
    ports:
      - "3000:3000"
    environment:
      - MONGO_URL=mongodb://mongo:27017/mydb
    depends_on:
      - mongo
  mongo:
    image: mongo
    container_name: mongo
    ports:
      - "27017:27017"
    volumes:
      - mongodb_data:/data/db

volumes:
  mongodb_data:
```

📌 En tu código de Node, debes usar:

```js
const url = process.env.MONGO_URL;
// Conectar con MongoDB usando esa URL
```

### Ejecutar:

```bash
docker-compose up --build
```

---

## 🌍 Caso 2: Frontend Vite + Backend + PostgreSQL

### 📁 Estructura general

```
project/
├── frontend/
│   ├── Dockerfile
│   └── (archivos de Vite)
├── backend/
│   ├── Dockerfile
│   └── (archivos Node.js)
└── docker-compose.yml
```

---

### 🔧 `frontend/Dockerfile`

```Dockerfile
FROM node:18

WORKDIR /app
COPY . .
RUN npm install
RUN npm run build

# Usar servidor estático para producción
RUN npm install -g serve
EXPOSE 4173
CMD ["serve", "-s", "dist"]
```

---

### 🔧 `backend/Dockerfile`

(igual que antes, pero conectando a PostgreSQL)

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

### 🔧 `docker-compose.yml` completo:

```yaml
version: '3.8'

services:
  frontend:
    build: ./frontend
    container_name: frontend
    ports:
      - "5173:4173"
    depends_on:
      - backend

  backend:
    build: ./backend
    container_name: backend
    ports:
      - "3000:3000"
    environment:
      - PG_HOST=db
      - PG_PORT=5432
      - PG_USER=postgres
      - PG_PASSWORD=1234
      - PG_DB=mydb
    depends_on:
      - db

  db:
    image: postgres
    container_name: postgres
    restart: always
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: 1234
      POSTGRES_DB: mydb
    volumes:
      - pgdata:/var/lib/postgresql/data
    ports:
      - "5432:5432"

volumes:
  pgdata:
```

✅ En tu backend deberías usar:

```js
const { Pool } = require('pg');
const pool = new Pool({
  host: process.env.PG_HOST,
  port: process.env.PG_PORT,
  user: process.env.PG_USER,
  password: process.env.PG_PASSWORD,
  database: process.env.PG_DB,
});
```

---

## ▶️ Cómo levantar todo el entorno

Desde la raíz del proyecto:

```bash
docker-compose up --build
```

Todo estará funcionando en:

* 🖥️ Frontend: `http://localhost:5173`
* 🔗 API Backend: `http://localhost:3000`
* 🛢️ PostgreSQL: `localhost:5432`

---

# ✅ Conclusión

| Nivel      | Herramienta      | Uso                                                                 |
| ---------- | ---------------- | ------------------------------------------------------------------- |
| Básico     | `docker run`     | Crear y correr contenedores manualmente                             |
| Intermedio | `Dockerfile`     | Crear imágenes personalizadas                                       |
| Avanzado   | `Docker Compose` | Orquestar múltiples servicios (microservicios, frontend/backend/db) |

---

¿Quieres que prepare un **proyecto completo funcional listo para clonar** o un PDF imprimible de toda esta guía? También puedo armar un paso a paso como ejercicio práctico guiado.
