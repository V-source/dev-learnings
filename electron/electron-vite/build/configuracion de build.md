# Explicación Sencilla de Configuración de Build en Electron-Vite

## 🔧 Personalizando el Build
Electron-Vite te permite ajustar cómo se compila tu app modificando las opciones de Rollup (la herramienta que usa internamente):

```javascript
// electron.vite.config.js
export default defineConfig({
  main: { /* configuración para el proceso principal */ },
  preload: { /* configuración para preload scripts */ },
  renderer: { /* configuración para el frontend (React) */ }
})
```

## 🧩 Estrategia de Chunking (División de código)
Es importante dividir el código en partes (chunks) para que tu app sea más rápida:

```javascript
output: {
  manualChunks(id) {
    if (id.includes('foo')) {
      return 'foo' // Separa todo lo de 'foo' en un archivo aparte
    }
  }
}
```
**Ejemplo práctico:**  
Si tienes una librería grande como `lodash`, puedes separarla en su propio chunk para que no se cargue con el código principal.

## 📦 Externals (Qué NO incluir en el build)
Hay cosas que NO deben incluirse en el build final, especialmente en Electron:

```javascript
external: ['sqlite3'] // No incluyas sqlite3 en el bundle
```

**¿Por qué?**  
Porque son módulos de Node que ya existen en Electron. Si los incluyes, causarán errores.

## 🔌 Plugin Automático (Recomendado)
En vez de poner cada dependencia manualmente, usa este plugin:

```javascript
import { externalizeDepsPlugin } from 'electron-vite'

plugins: [externalizeDepsPlugin()] // Automáticamente excluye dependencias
```

## 💡 Mejores Prácticas
1. **Proceso principal y preloads**:  
   Usa `externalizeDepsPlugin` para hacer el paquete más pequeño.

2. **Frontend (React)**:  
   Incluye todo en el bundle (instala librerías como `devDependencies`).

3. **Módulos nativos**:  
   Siempre decláralos como `external` (sqlite3, fs, path, etc.).

**Ejemplo completo recomendado:**
```javascript
export default defineConfig({
  main: {
    plugins: [externalizeDepsPlugin()] // Para el proceso principal
  },
  renderer: {
    // Aquí no necesitas externalizar (React se bundlea completo)
  }
})
```

Esto hará que tu app de Electron sea más eficiente y evita errores comunes con módulos nativos. 🚀
