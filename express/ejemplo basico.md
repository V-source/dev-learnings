Un script completo para un servidor en **Express** con **TypeScript** que incluye todas las funcionalidades solicitadas. Este servidor maneja errores, se conecta a **MongoDB** y **SQL** (usando **MySQL** como ejemplo), implementa autenticación con **JSON Web Tokens (JWT)**, incluye un logger para el servidor y la base de datos, limita las peticiones, maneja sesiones y tokens de sesión, y define middlewares para las rutas de **login**, **dashboard** y **logout**.

---

### **Estructura del Proyecto**
```
src/
├── config/
│   ├── db.ts
│   └── logger.ts
├── controllers/
│   ├── authController.ts
│   └── dashboardController.ts
├── middlewares/
│   ├── authMiddleware.ts
│   ├── errorHandler.ts
│   ├── rateLimiter.ts
│   └── sessionMiddleware.ts
├── models/
│   ├── mongoModel.ts
│   └── sqlModel.ts
├── routes/
│   ├── authRoutes.ts
│   └── dashboardRoutes.ts
├── services/
│   ├── authService.ts
│   └── loggerService.ts
├── types/
│   └── customTypes.ts
├── app.ts
└── server.ts
```

---

### **Código del Servidor**

#### **1. Dependencias necesarias**
Instala las dependencias necesarias:
```bash
npm install express typescript ts-node dotenv mongoose mysql2 jsonwebtoken express-session rate-limiter-flexible winston cors
npm install --save-dev @types/express @types/node @types/cors @types/jsonwebtoken @types/express-session
```

---

#### **2. Configuración de TypeScript (`tsconfig.json`)**
```json
{
  "compilerOptions": {
    "target": "ES6",
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "outDir": "./dist"
  },
  "include": ["src/**/*.ts"],
  "exclude": ["node_modules"]
}
```

---

#### **3. Archivo de configuración (`src/config/db.ts`)**
```typescript
import mongoose from 'mongoose';
import mysql from 'mysql2/promise';

// Conexión a MongoDB
export const connectMongoDB = async () => {
  try {
    await mongoose.connect(process.env.MONGO_URI!);
    console.log('MongoDB connected');
  } catch (error) {
    console.error('MongoDB connection error:', error);
  }
};

// Conexión a MySQL
export const connectMySQL = async () => {
  try {
    const connection = await mysql.createConnection({
      host: process.env.MYSQL_HOST,
      user: process.env.MYSQL_USER,
      password: process.env.MYSQL_PASSWORD,
      database: process.env.MYSQL_DATABASE,
    });
    console.log('MySQL connected');
    return connection;
  } catch (error) {
    console.error('MySQL connection error:', error);
  }
};
```

---

#### **4. Logger (`src/config/logger.ts`)**
```typescript
import winston from 'winston';

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: 'server.log' }),
  ],
});

export default logger;
```

---

#### **5. Middlewares (`src/middlewares/`)**
- **`authMiddleware.ts`**:
  ```typescript
  import { Request, Response, NextFunction } from 'express';
  import jwt from 'jsonwebtoken';

  export const authMiddleware = (req: Request, res: Response, next: NextFunction) => {
    const token = req.header('Authorization')?.replace('Bearer ', '');
    if (!token) return res.status(401).json({ message: 'Access denied' });

    try {
      const decoded = jwt.verify(token, process.env.JWT_SECRET!);
      (req as any).user = decoded;
      next();
    } catch (error) {
      res.status(400).json({ message: 'Invalid token' });
    }
  };
  ```

- **`rateLimiter.ts`**:
  ```typescript
  import { RateLimiterMemory } from 'rate-limiter-flexible';

  const rateLimiter = new RateLimiterMemory({
    points: 10, // 10 solicitudes
    duration: 1, // por segundo
  });

  export const rateLimiterMiddleware = (req: Request, res: Response, next: NextFunction) => {
    rateLimiter.consume(req.ip)
      .then(() => next())
      .catch(() => res.status(429).json({ message: 'Too many requests' }));
  };
  ```

- **`sessionMiddleware.ts`**:
  ```typescript
  import session from 'express-session';

  export const sessionMiddleware = session({
    secret: process.env.SESSION_SECRET!,
    resave: false,
    saveUninitialized: true,
    cookie: { secure: false, maxAge: 1000 * 60 * 60 }, // 1 hora
  });
  ```

---

#### **6. Rutas (`src/routes/`)**
- **`authRoutes.ts`**:
  ```typescript
  import express from 'express';
  import { login, logout } from '../controllers/authController';

  const router = express.Router();

  router.post('/login', login);
  router.post('/logout', logout);

  export default router;
  ```

- **`dashboardRoutes.ts`**:
  ```typescript
  import express from 'express';
  import { authMiddleware } from '../middlewares/authMiddleware';
  import { getDashboard } from '../controllers/dashboardController';

  const router = express.Router();

  router.get('/dashboard', authMiddleware, getDashboard);

  export default router;
  ```

---

#### **7. Servidor (`src/server.ts`)**
```typescript
import express from 'express';
import cors from 'cors';
import { connectMongoDB, connectMySQL } from './config/db';
import { sessionMiddleware } from './middlewares/sessionMiddleware';
import { rateLimiterMiddleware } from './middlewares/rateLimiter';
import authRoutes from './routes/authRoutes';
import dashboardRoutes from './routes/dashboardRoutes';
import { errorHandler } from './middlewares/errorHandler';

const app = express();

// Middlewares
app.use(cors());
app.use(express.json());
app.use(sessionMiddleware);
app.use(rateLimiterMiddleware);

// Rutas
app.use('/auth', authRoutes);
app.use('/dashboard', dashboardRoutes);

// Manejo de errores
app.use(errorHandler);

// Iniciar servidor
const PORT = process.env.PORT || 3000;
app.listen(PORT, async () => {
  await connectMongoDB();
  await connectMySQL();
  console.log(`Server running on port ${PORT}`);
});
```

---

### **Variables de Entorno (`.env`)**
```env
MONGO_URI=mongodb://localhost:27017/mydatabase
MYSQL_HOST=localhost
MYSQL_USER=root
MYSQL_PASSWORD=password
MYSQL_DATABASE=mydatabase
JWT_SECRET=mysecretkey
SESSION_SECRET=mysessionsecret
```

---

### **Ejecución**
1. Compila el proyecto:
   ```bash
   tsc
   ```
2. Inicia el servidor:
   ```bash
   node dist/server.js
   ```

---

