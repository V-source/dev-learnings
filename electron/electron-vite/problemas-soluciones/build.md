# PROBLEMA CON EL BUILD PARA WINDOWS (npm run build:win).


```bash
> npm run build && electron-builder --win


> sucrenet-autogestion@1.0.0 build
> npm run typecheck && electron-vite build


> sucrenet-autogestion@1.0.0 typecheck
> npm run typecheck:node && npm run typecheck:web


> sucrenet-autogestion@1.0.0 typecheck:node
> tsc --noEmit -p tsconfig.node.json --composite false

electron.vite.config.ts:4:25 - error TS2307: Cannot find module '@tailwindcss/vite' or its corresponding type declarations.
  There are types at '/home/villegas/git/pago-rapido/node_modules/@tailwindcss/vite/dist/index.d.mts', but this result could not be resolved under your current 'moduleResolution' setting. Consider updat
ing to 'node16', 'nodenext', or 'bundler'.

4 import tailwindcss from '@tailwindcss/vite'
                          ~~~~~~~~~~~~~~~~~~~


Found 1 error in electron.vite.config.ts:4
```

### codigo original (causa del problema).

```ts tsconfig.node.json (original).
{
  "extends": "@electron-toolkit/tsconfig/tsconfig.node.json",
  "include": ["electron.vite.config.*", "src/main/**/*", "src/preload/**/*"],
  "compilerOptions": {
    "composite": true,
    "types": ["electron-vite/node"]
  }
}
```

### codigo modificado (solucion al problema).
```ts tsconfig.node.json (modificado - solucion).
{
  "extends": "@electron-toolkit/tsconfig/tsconfig.node.json",
  "include": ["electron.vite.config.*", "src/main/**/*", "src/preload/**/*"],
  "compilerOptions": {
    "composite": true,
    "types": ["electron-vite/node", "@tailwindcss/vite"],
    "moduleResolution": "bundler",
    "esModuleInterop": true,
    "skipLibCheck": true
  }
}

```



---

### 🧩 **Causa del problema**

Al ejecutar el comando de build (`npm run build:win`), TypeScript lanzó este error:

```
Cannot find module '@tailwindcss/vite' or its corresponding type declarations.
```

Esto ocurre porque TypeScript no puede encontrar el módulo `@tailwindcss/vite`, **a pesar de que existe** en `node_modules`.
El problema se debe a la **configuración del `tsconfig`**:

* El **`moduleResolution`** no está configurado correctamente para entender archivos `.mts` (usados por ese módulo).
* Además, no se le indicó explícitamente a TypeScript que incluyera los tipos de ese paquete.

---

### ✅ **Solución implementada**

#### Paso 1: Instalar los tipos (aunque opcional en este caso, ayuda al autocompletado)

```bash
npm install --save-dev @types/tailwindcss
```

#### Paso 2: Modificar el archivo `tsconfig.node.json` (o el usado en el build)

Código original:

```json
{
  "extends": "@electron-toolkit/tsconfig/tsconfig.node.json",
  "include": ["electron.vite.config.*", "src/main/**/*", "src/preload/**/*"],
  "compilerOptions": {
    "composite": true,
    "types": ["electron-vite/node"]
  }
}
```

Código corregido:

```json
{
  "extends": "@electron-toolkit/tsconfig/tsconfig.node.json",
  "include": ["electron.vite.config.*", "src/main/**/*", "src/preload/**/*"],
  "compilerOptions": {
    "composite": true,
    "types": ["electron-vite/node", "@tailwindcss/vite"],
    "moduleResolution": "bundler",
    "esModuleInterop": true,
    "skipLibCheck": true
  }
}
```

---

### 🔍 **¿Qué cambiaste y por qué?**

* `"types": [...]`: agregaste `@tailwindcss/vite` para que TypeScript sepa buscar sus tipos.
* `"moduleResolution": "bundler"`: esto permite resolver módulos `.mts`, `.cts`, etc., usados por Vite y Tailwind.
* `"esModuleInterop": true` y `"skipLibCheck": true`: mejoras de compatibilidad para evitar errores por tipos de librerías externas.

---

### 🧠 Conclusión

Este error no es por falta de instalación, sino por una **configuración incompleta de TypeScript**.
Con los cambios aplicados, TypeScript puede encontrar y compilar correctamente los módulos modernos como `@tailwindcss/vite`.

---

