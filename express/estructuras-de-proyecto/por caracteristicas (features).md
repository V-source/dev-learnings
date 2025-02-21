La estructura de carpetas por características (también conocida como **estructura modular por características**) es una forma de organizar un proyecto de ExpressJS (o cualquier aplicación Node.js) en la que los archivos se agrupan según la funcionalidad o característica que implementan, en lugar de organizarlos por tipo de archivo (por ejemplo, controllers, models, routes, etc.). Este enfoque es especialmente útil en proyectos grandes, ya que mejora la mantenibilidad, escalabilidad y legibilidad del código.

### ¿Por qué usar una estructura de carpetas por características?

1. **Mantenibilidad**: Al agrupar todo lo relacionado con una característica en una sola carpeta, es más fácil encontrar y modificar el código relacionado con esa funcionalidad.
2. **Escalabilidad**: Facilita la adición de nuevas características sin afectar el resto del proyecto.
3. **Legibilidad**: El código está mejor organizado y es más fácil de entender para nuevos desarrolladores.
4. **Desacoplamiento**: Cada característica es independiente, lo que reduce las dependencias entre módulos.

---

### Estructura de Carpetas por Características

A continuación, te muestro un ejemplo de cómo podría estructurarse un proyecto de ExpressJS utilizando este enfoque:

```
mi-proyecto/
│
├── src/
│   ├── features/
│   │   ├── auth/
│   │   │   ├── auth.controller.js
│   │   │   ├── auth.routes.js
│   │   │   ├── auth.service.js
│   │   │   ├── auth.model.js
│   │   │   └── auth.middleware.js
│   │   ├── users/
│   │   │   ├── users.controller.js
│   │   │   ├── users.routes.js
│   │   │   ├── users.service.js
│   │   │   └── users.model.js
│   │   ├── products/
│   │   │   ├── products.controller.js
│   │   │   ├── products.routes.js
│   │   │   ├── products.service.js
│   │   │   └── products.model.js
│   │   └── orders/
│   │       ├── orders.controller.js
│   │       ├── orders.routes.js
│   │       ├── orders.service.js
│   │       └── orders.model.js
│   ├── middlewares/
│   │   └── globalMiddleware.js
│   ├── utils/
│   │   └── helpers.js
│   ├── config/
│   │   └── db.js
│   ├── app.js
│   └── server.js
│
├── .env
├── package.json
└── README.md
```

---

### Explicación de la Estructura

1. **`src/`**: Es la carpeta principal donde se encuentra todo el código fuente del proyecto.
2. **`features/`**: Dentro de esta carpeta, se agrupan todas las características o funcionalidades del proyecto. Cada característica tiene su propia carpeta.
   - **`auth/`**: Contiene todo lo relacionado con la autenticación (controladores, rutas, servicios, modelos, etc.).
   - **`users/`**: Contiene todo lo relacionado con la gestión de usuarios.
   - **`products/`**: Contiene todo lo relacionado con la gestión de productos.
   - **`orders/`**: Contiene todo lo relacionado con la gestión de pedidos.
3. **`middlewares/`**: Aquí se colocan los middlewares globales que se aplican a toda la aplicación (por ejemplo, manejo de errores, autenticación global, etc.).
4. **`utils/`**: Contiene funciones de utilidad que pueden ser reutilizadas en todo el proyecto (por ejemplo, funciones de validación, formateo de fechas, etc.).
5. **`config/`**: Aquí se colocan los archivos de configuración, como la conexión a la base de datos, variables de entorno, etc.
6. **`app.js`**: Es el archivo principal de la aplicación, donde se configuran los middlewares globales y se montan las rutas.
7. **`server.js`**: Es el archivo que inicia el servidor.

---

### Ejemplo de Código

#### 1. **`auth/auth.routes.js`**
```javascript
import express from 'express';
import { login, register } from './auth.controller.js';

const router = express.Router();

router.post('/login', login);
router.post('/register', register);

export default router;
```

#### 2. **`auth/auth.controller.js`**
```javascript
import { AuthService } from './auth.service.js';

export const login = async (req, res) => {
    const { email, password } = req.body;
    const token = await AuthService.login(email, password);
    res.json({ token });
};

export const register = async (req, res) => {
    const { email, password } = req.body;
    const user = await AuthService.register(email, password);
    res.json({ user });
};
```

#### 3. **`auth/auth.service.js`**
```javascript
import { UserModel } from '../users/users.model.js';

export class AuthService {
    static async login(email, password) {
        // Lógica de autenticación
    }

    static async register(email, password) {
        // Lógica de registro
    }
}
```

#### 4. **`app.js`**
```javascript
import express from 'express';
import authRoutes from './features/auth/auth.routes.js';
import userRoutes from './features/users/users.routes.js';
import globalMiddleware from './middlewares/globalMiddleware.js';

const app = express();

app.use(express.json());
app.use(globalMiddleware);

app.use('/auth', authRoutes);
app.use('/users', userRoutes);

export default app;
```

#### 5. **`server.js`**
```javascript
import app from './app.js';
import { connectDB } from './config/db.js';

const PORT = process.env.PORT || 3000;

connectDB().then(() => {
    app.listen(PORT, () => {
        console.log(`Servidor corriendo en http://localhost:${PORT}`);
    });
});
```

---

### Ventajas de esta Estructura

1. **Claridad**: Cada característica está encapsulada en su propia carpeta, lo que facilita la navegación y comprensión del código.
2. **Reutilización**: Los servicios y modelos pueden ser reutilizados en diferentes partes del proyecto.
3. **Pruebas**: Facilita la escritura de pruebas unitarias y de integración, ya que cada característica está aislada.
4. **Colaboración**: Diferentes desarrolladores pueden trabajar en características distintas sin interferir entre sí.

---

### Conclusión

La estructura de carpetas por características es una excelente opción para proyectos de ExpressJS que buscan escalar y mantenerse organizados. Al agrupar el código por funcionalidad, se mejora la legibilidad, mantenibilidad y desacoplamiento del proyecto. Esta estructura es especialmente útil en aplicaciones grandes con múltiples características y equipos de desarrollo.

