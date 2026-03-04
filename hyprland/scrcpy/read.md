# Configuración y Uso de Scrcpy en Hyprland

## 1. Instalación (Arch Linux)
Instala los paquetes necesarios desde los repositorios oficiales:
```bash
sudo pacman -S scrcpy libusb android-tools

```

## 2. Preparación del Dispositivo Android

1. Activar **Opciones de desarrollador** (Ajustes > Acerca del teléfono > 7 clics en "Número de compilación").
2. Activar **Depuración USB**.
3. (Solo Xiaomi) Activar **Depuración USB (Ajustes de seguridad)** para control con ratón.
4. Conectar por USB y aceptar el mensaje de confianza en el móvil.

## 3. Configuración en Hyprland (`hyprland.conf`)

Añade estas reglas y atajos para manejar las ventanas de scrcpy correctamente:

```conf
# Atajo para alternar flotado (Super + V)
bind = $mainMod, V, togglefloating

# Reglas de ventana para scrcpy
windowrule = float, ^(scrcpy)$
windowrule = center, ^(scrcpy)$

# Regla específica para la Webcam 1080p
windowrulev2 = float, title:^(Webcam Full HD)$
windowrulev2 = size 1920 1080, title:^(Webcam Full HD)$
windowrulev2 = center, title:^(Webcam Full HD)$

# Mouse bindings para mover y redimensionar
bindm = $mainMod, mouse:272, movewindow
bindm = $mainMod, mouse:273, resizewindow

```

## 4. Comandos de Ejecución

### Modo Espejo (Pantalla completa del móvil)

```bash
scrcpy --always-on-top --render-driver=opengl

```

### Modo Cámara Web Frontal (1920x1080)

Usa este comando para usar el teléfono como cámara de alta resolución:

```bash
scrcpy --video-source=camera --camera-id=1 --camera-size=1920x1080 --window-title="Webcam Full HD" --always-on-top

```

### Diagnóstico de Cámaras

Si el ID `1` o la resolución fallan, usa estos comandos para ver qué soporta tu hardware:

```bash
# Listar cámaras disponibles
scrcpy --list-cameras

# Listar resoluciones soportadas por la cámara frontal
scrcpy --list-camera-sizes --camera-id=1

```

## 5. Solución de Problemas Frecuentes

* **Error de permisos:** Asegúrate de que el servidor ADB esté corriendo con `adb devices`.
* **Lag en Wayland:** Utiliza siempre `--render-driver=opengl` para mejorar la fluidez.
* **Atajos en conflicto:** Scrcpy usa `Alt Izquierdo` como tecla modificadora para no interferir con la tecla `Super` de Hyprland.

```

¿Necesitas que te ayude con algo más para terminar de dejar tu entorno a punto?

