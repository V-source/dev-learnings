# Solución al Error de Electron-Builder en Linux (libcrypt.so.1)

## **Error y Tipo**  
**Error:**  
`error while loading shared libraries: libcrypt.so.1: cannot open shared object file: No such file or directory`  

**Tipo:**  
Fallo de dependencia del sistema durante el empaquetado (generación de paquete `.deb`).

---

## **Causas del Error**  
1. **Incompatibilidad de librerías**:  
   - Distribuciones modernas de Linux (ej. Arch Linux) usan `libxcrypt` en lugar de `libcrypt.so.1` (obsoleto).  
   - Electron-builder utiliza internamente `fpm`, una herramienta que depende de la versión antigua de `libcrypt`.  

2. **Configuración incompleta**:  
   - Falta de ícono personalizado (usa el ícono por defecto de Electron).  
   - Dependencias del sistema no instaladas para empaquetado en Linux.  

3. **Entorno de construcción**:  
   - El sistema no tiene las librerías necesarias para generar paquetes `.deb` correctamente.  

---

## **Soluciones**  

### **1. Instalar la librería faltante**  
**Para Arch Linux y derivados:**  
```bash
sudo pacman -S libxcrypt-compat
```  

---

**Para Debian/Ubuntu:**
```bash
sudo apt install libcrypt1
```

### 2. Usar formatos alternativos
Modificar `electron-builder.yml` para evitar `.deb`:
```yaml
linux:
  target: ["AppImage", "tar.gz"]  # Formatos más portables
```

### 3. Configurar ícono personalizado
Añadir en `electron-builder.yml`:
```yaml
icon: "build/icon.png"  # Asegurar que existen íconos en 16x16, 32x32, ..., 512x512
```

### 4. Instalar dependencias generales
```bash
sudo pacman -S gtk3 libnotify nss libxss libxtst xdg-utils at-spi2-core libsecret
```

### 5. Solución avanzada: Usar Docker
Si persiste el error, empaquetar en un contenedor con todas las dependencias:
```bash
docker run --rm -ti -v ${PWD}:/project -w /project electronuserland/builder:wine npm run build
```

---

## Conclusión
El error se resuelve instalando `libxcrypt-compat` (o `libcrypt1`) para compatibilidad con `fpm`, junto con configurar correctamente el entorno de construcción y los recursos de la aplicación.
```
