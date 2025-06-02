# Configuración de TypeScript en un Proyecto Node.js

Para usar TypeScript en tu proyecto Node.js, sigue estos pasos:

## 1. Inicialización básica

```bash
# Inicia tu proyecto Node.js (si no lo has hecho)
npm init -y

# Instala TypeScript como dependencia de desarrollo
npm install --save-dev typescript

# Instala los tipos para Node.js
npm install --save-dev @types/node
```

## 2. Configuración de TypeScript

Crea un archivo `tsconfig.json` en la raíz de tu proyecto:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "moduleResolution": "node",
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```

## 3. Estructura de directorios recomendada

```
tu-proyecto/
├── src/
│   ├── index.ts         # Punto de entrada
│   ├── controllers/     # Controladores
│   ├── services/        # Lógica de negocio
│   └── types/           # Tipos personalizados
├── dist/                # Código compilado (se crea automáticamente)
├── tsconfig.json        # Configuración TypeScript
└── package.json
```

## 4. Scripts esenciales en package.json

```json
{
  "scripts": {
    "build": "tsc",
    "dev": "ts-node src/index.ts",
    "start": "node dist/index.js",
    "watch": "tsc-watch --onSuccess \"node dist/index.js\""
  }
}
```

## 5. Dependencias de desarrollo adicionales

```bash
# Para ejecución en desarrollo sin compilar
npm install --save-dev ts-node

# Para recarga automática
npm install --save-dev ts-node-dev

# O para watch mode avanzado
npm install --save-dev tsc-watch
```

## 6. Ejemplo básico de código TypeScript

`src/index.ts`:
```typescript
import express, { Application, Request, Response } from 'express';

interface User {
  id: number;
  name: string;
}

const app: Application = express();
const PORT: number = 3000;

app.get('/', (req: Request, res: Response) => {
  const user: User = { id: 1, name: 'John Doe' };
  res.send(`Hello ${user.name}`);
});

app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

## 7. Configuración para ESLint (opcional pero recomendado)

```bash
npm install --save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
```

`.eslintrc.json`:
```json
{
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "ecmaVersion": 2020,
    "sourceType": "module"
  },
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended"
  ],
  "rules": {
    "@typescript-eslint/explicit-function-return-type": "warn"
  }
}
```

## 8. Tipos personalizados

Crea archivos `.d.ts` o `.ts` en tu carpeta `types/`:

`types/custom.d.ts`:
```typescript
declare namespace NodeJS {
  interface ProcessEnv {
    NODE_ENV: 'development' | 'production' | 'test';
    PORT?: string;
  }
}
```

## 9. Producción

Para preparar tu proyecto para producción:
```bash
npm run build  # Compila a JavaScript
npm start      # Ejecuta el código compilado
```

## Consejos adicionales

1. Usa `import/export` en lugar de `require/module.exports`
2. Configura paths absolutos en `tsconfig.json` para evitar imports relativos largos
3. Considera usar `nodemon` con `ts-node` para mejor experiencia en desarrollo:
   ```bash
   npm install --save-dev nodemon
   ```
   Y en package.json:
   ```json
   "dev": "nodemon --exec ts-node src/index.ts"
   ```

Esta configuración te proporcionará un entorno TypeScript robusto para Node.js con todas las ventajas del tipado estático.
