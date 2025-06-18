# Solución al Error de Electron-Builder para Windows en Linux

## **Error Detectado**
```bash
⨯ wine is required, please see https://electron.build/multi-platform-build#linux
⨯ /home/villegas/git/pago-rapido/node_modules/app-builder-bin/linux/x64/app-builder process failed ERR_ELECTRON_BUILDER_CANNOT_EXECUTE
```


## **Causas del Problema**
1. **Falta de Wine**:  
   - Electron-builder necesita Wine para empaquetar aplicaciones Windows desde Linux
   - El sistema no tiene instalado Wine o no está configurado correctamente

2. **Configuración incompleta**:
   - No se especificó el target de Windows en la configuración
   - Falta de ícono personalizado para Windows

3. **Entorno de construcción**:
   - Dependencias faltantes para compilación cruzada

---

## **Soluciones Paso a Paso**

### 1. Instalar Wine (Requisito esencial)
```bash
# Para Arch Linux:
sudo pacman -S wine-staging winetricks

# Para Debian/Ubuntu:
sudo apt install wine-stable
```

### 2. Configurar electron-builder.yml
```yaml
# Añadir/Modificar en tu archivo de configuración:
win:
  target: ["nsis", "portable"]  # Formatos para Windows
  icon: "build/icons/icon.ico"  # Ícono en formato .ico
```

### 3. Instalar dependencias adicionales
```bash
# Instalar herramientas de compilación cruzada
sudo pacman -S binutils-mingw-w64 gcc-mingw-w64
```

### 4. Ejecutar la construcción correctamente
```bash
# Especificar explícitamente la plataforma:
npm run build -- --win --x64

# Alternativa usando Docker (recomendada para builds consistentes):
docker run --rm -ti \
  -v ${PWD}:/project \
  -v ~/.cache/electron:/root/.cache/electron \
  -v ~/.cache/electron-builder:/root/.cache/electron-builder \
  electronuserland/builder:wine \
  /bin/bash -c "npm install && npm run build -- --win --x64"
```

### 5. Solucionar problemas de íconos
- Asegúrate de tener un archivo `.ico` en múltiples resoluciones (256x256 como mínimo)
- Usa herramientas como `icotool` o convertidores en línea para generarlo

---

## **Conclusión**
1. Wine es **obligatorio** para builds de Windows desde Linux
2. La configuración debe incluir targets específicos para Windows
3. Docker proporciona un entorno más confiable para builds multiplataforma

**Recomendación final:**  
Usa el contenedor Docker oficial de electron-builder para evitar problemas de dependencias en tu sistema local.
