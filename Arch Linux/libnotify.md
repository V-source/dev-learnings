# OPCIONES (FLAGS)
###  Tiempo de duración

```bash
notify-send -t 5000 "Título" "Mensaje"  # 5 segundos
```

El parámetro `-t` especifica el tiempo en milisegundos. Por ejemplo:

- `-t 5000`: 5 segundos
- `-t 10000`: 10 segundos
- `-t 30000`: 30 segundos

###  Nivel de urgencia

```bash
notify-send -u critical "¡Importante!" "Mensaje urgente"  # Máxima prioridad
notify-send -u normal "Notificación" "Mensaje normal"      # Prioridad normal
notify-send -u low "Info" "Mensaje de baja prioridad"     # Baja prioridad
```

###  Iconos

```bash
notify-send -i notification-message "Título" "Mensaje"  # Icono de mensaje
notify-send -i dialog-warning "¡Cuidado!" "Mensaje"        # Icono de advertencia
notify-send -i dialog-information "Info" "Mensaje"         # Icono informativo
```

###  Categorías

```bash
notify-send -c email "Nuevo correo" "Has recibido un mensaje"  # Para notificaciones de email
notify-send -c network "Red" "Conexión establecida"         # Para notificaciones de red
notify-send -c transfer "Descarga" "Completa"              # Para notificaciones de transferencia
```

###  Ejemplo completo

```bash
notify-send \
  -t 5000 \                    # Duración de 5 segundos
  -u normal \                  # Prioridad normal
  -i notification-message \    # Icono de mensaje
  -c email \                   # Categoría de email
  "Nuevo correo" \             # Título
  "Has recibido un mensaje"    # Mensaje
```

###  Nombre de la aplicación
El flag `-a` (o `--app-name`) en notify-send especifica el nombre de la aplicación que está enviando la notificación 20:0. Este nombre se usa para identificar la aplicación que está generando la notificación y puede ser útil para:

- Identificar visualmente las notificaciones en la interfaz
- Permitir que el sistema de notificaciones agrupe las notificaciones por aplicación
- Facilitar la gestión de permisos de notificación por aplicación

Ejemplo de uso:

```bash
notify-send -a "MiAplicacion" "Título" "Mensaje"
```

Este flag es especialmente útil cuando estás creando scripts o aplicaciones que necesitan enviar notificaciones y quieres que sean identificables como provenientes de tu aplicación específica.

---
---

# DISEÑO Y ESTILOS

Para editar el diseño de las notificaciones en i3, necesitas configurar dunst, que es el servidor de notificaciones más común para i3. Aquí te muestro cómo hacerlo:

 Primero, copia el archivo de configuración de ejemplo
```bash
cp /usr/share/dunst/dunstrc ~/.config/dunst/dunstrc
```

 Edita el archivo con tu editor preferido
```bash
nano ~/.config/dunst/dunstrc
```

 Configura los colores y estilos
```bash
[urgency_low]
    background = "#222222"
    foreground = "#888888"
    timeout = 10

[urgency_normal]
    background = "#285577"
    foreground = "#ffffff"
    timeout = 10

[urgency_critical]
    background = "#900000"
    foreground = "#ffffff"
    timeout = 0

[settings]
    font = "Monospace 12"
    format = "%s\n%b"
    alignment = "center"
    show_age_threshold = 60
    indicate_hidden = true
    geometry = "300x50-30+30"
    transparency = 15
    separator_height = 2
    padding = 8
    horizontal_padding = 8
    separator_color = "frame"
    frame_width = 2
    frame_color = "frame"
    sort = true
```

 Reinicia dunst para aplicar los cambios
```bash
killall dunst
dunst &
```

Explicación de las opciones principales:

- `background`: Color de fondo de la notificación
- `foreground`: Color del texto
- `font`: Fuente y tamaño del texto
- `geometry`: Formato "anchoxalto+posicion_x+posicion_y"
- `transparency`: Nivel de transparencia (0-100)
- `padding`: Espacio entre el borde y el texto
- `frame_width`: Grosor del borde
- `frame_color`: Color del borde

Puedes usar cualquier color en formato hexadecimal (ej: `#RRGGBB`) o `#RRGGBBAA` para incluir transparencia.


---
---

# NOTIFICACIONES CON BOTONES

Para crear notificaciones con botones que ejecuten scripts en bash, necesitarás usar dunstify en lugar de notify-send directamente. Aquí te muestro cómo hacerlo:

 Primero, asegúrate de tener dunst instalado
```bash
sudo pacman -S dunst
```

 Ejemplo básico de notificación con botones

```bash
dunstify -a "si,Sí" -a "no,No" "¿Deseas ejecutar el script?"
```

 Script completo que maneja las acciones
```bash
#!/bin/bash

# Mostrar la notificación con botones
dunstify -a "ejecutar,Ejecutar" -a "cancelar,Cancelar" "¿Deseas ejecutar el script?"

# Capturar la acción seleccionada
read -r action

case $action in
    "ejecutar")
        # Ejecutar tu script aquí
        ./tu_script.sh
        ;;
    "cancelar")
        echo "Operación cancelada"
        ;;
esac
```

Opciones adicionales útiles:

```bash
dunstify \
  -a "si,Sí" -a "no,No" \
  -t 10000 \                    # Tiempo en ms antes de desaparecer
  -u normal \                   # Nivel de urgencia
  "¿Deseas ejecutar el script?" \
  "Este es el mensaje detallado"
```

Para ejecutar scripts específicos con cada botón:

```bash
#!/bin/bash

# Mostrar la notificación
dunstify -a "script1,Ejecutar Script 1" -a "script2,Ejecutar Script 2" "Seleccione un script"

read -r action

case $action in
    "script1")
        ./script1.sh
        ;;
    "script2")
        ./script2.sh
        ;;
esac
```
