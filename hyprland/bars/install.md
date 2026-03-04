Para usar **Waybar** en Hyprland, necesitas instalar el paquete, configurar su inicio automático y personalizar los archivos de diseño (layout) y estilo (CSS). A diferencia de las barras tradicionales, Waybar es altamente flexible y se basa en módulos.

Sigue estos pasos para dejarla funcional:

---

### 1. Instalación

En Arch Linux, lo ideal es instalar la versión que incluye soporte para las funciones específicas de Hyprland (como los workspaces dinámicos).

```bash
sudo pacman -S waybar

```

*Nota: Si quieres iconos bonitos (batería, wifi, volumen), asegúrate de tener instalada una Nerd Font (como `ttf-jetbrains-mono-nerd`).*

---

### 2. Estructura de archivos

Waybar busca su configuración en `~/.config/waybar/`. Necesitas crear dos archivos principales:

1. **`config.jsonc`**: Define qué módulos aparecen y dónde (reloj, batería, etc.).
2. **`style.css`**: Define los colores, bordes y fuentes (como si fuera una página web).

---

### 3. Configuración básica (Ejemplo rápido)

Crea el archivo `~/.config/waybar/config.jsonc` y pega esto para empezar:

```jsonc
{
    "layer": "top", // Aparece por encima de las ventanas
    "position": "top", // En la parte superior
    "height": 30,
    "modules-left": ["hyprland/workspaces", "hyprland/mode"],
    "modules-center": ["hyprland/window"],
    "modules-right": ["pulseaudio", "network", "battery", "clock", "tray"],

    "hyprland/workspaces": {
        "disable-scroll": true,
        "all-outputs": true
    },
    "clock": {
        "format": "{:%H:%M}  ",
        "tooltip-format": "<big>{:%Y %B}</big>\n<tt><small>{calendar}</small></tt>"
    }
}

```

---

### 4. Autoejecución en Hyprland

Para que la barra aparezca apenas inicies sesión, añade esta línea a tu `~/.config/hypr/hyprland.conf`:

```conf
exec-once = waybar

```

---

### 5. Comandos útiles para personalización

| Acción | Comando |
| --- | --- |
| **Recargar Waybar** | `killall waybar && waybar &` |
| **Ver errores de config** | Lánzalo desde la terminal: `waybar` |
| **Identificar monitores** | `hyprctl monitors` |

---

### 6. El truco del "Refresco Automático"

Si modificas el archivo `style.css`, verás que Waybar no se actualiza solo. Puedes crear un atajo de teclado en tu `hyprland.conf` para reiniciarlo rápidamente mientras experimentas con el diseño:

```conf
# Presiona Super + Alt + B para refrescar la barra
bind = $mainMod ALT, B, exec, killall waybar && waybar

```

### ¿Qué sigue?


Aquí tienes una configuración **minimalista, moderna y con bordes redondeados** para que la pegues en `~/.config/waybar/style.css`.

---

### 1. El código CSS (Copia y pega)

```css
/* --- Estilo Minimalista para Waybar --- */

* {
    /* Fuente recomendada: JetBrainsMono Nerd Font */
    font-family: "JetBrainsMono Nerd Font", Roboto, Helvetica, Arial, sans-serif;
    font-size: 13px;
    border: none;
    border-radius: 0;
}

window#waybar {
    background-color: rgba(30, 30, 46, 0.5); /* Color oscuro con transparencia (Hex: #1e1e2e) */
    border-bottom: 2px solid rgba(255, 255, 255, 0.1);
    color: #cdd6f4;
    transition-property: background-color;
    transition-duration: .5s;
}

/* --- Módulos individuales --- */

#workspaces button {
    padding: 0 5px;
    background-color: transparent;
    color: #fab387;
    border-bottom: 3px solid transparent;
    transition: all 0.3s ease;
}

#workspaces button.active {
    background-color: #313244;
    color: #89b4fa;
    border-bottom: 3px solid #89b4fa;
    border-radius: 5px;
}

#workspaces button:hover {
    background-color: rgba(137, 180, 250, 0.2);
    box-shadow: inherit;
    text-shadow: inherit;
}

#clock,
#battery,
#cpu,
#memory,
#disk,
#temperature,
#backlight,
#network,
#pulseaudio,
#wireplumber,
#custom-media,
#tray,
#mode,
#idle_inhibitor,
#scratchpad,
#power-profiles-daemon,
#mpd {
    padding: 0 10px;
    color: #cdd6f4;
    background-color: #313244;
    border-radius: 8px;
    margin: 4px 2px;
}

/* --- Colores específicos para destacar --- */

#clock {
    color: #f5c2e7; /* Rosa suave */
    font-weight: bold;
}

#battery.charging, #battery.plugged {
    color: #a6e3a1; /* Verde */
}

#battery.critical:not(.charging) {
    background-color: #f38ba8; /* Rojo */
    color: #11111b;
    animation-name: blink;
    animation-duration: 0.5s;
    animation-timing-function: linear;
    animation-iteration-count: infinite;
    animation-direction: alternate;
}

@keyframes blink {
    to {
        background-color: #f38ba8;
        color: #ffffff;
    }
}

#pulseaudio {
    color: #89b4fa; /* Azul */
}

#network {
    color: #94e2d5; /* Turquesa */
}

```

---

### 2. Cómo aplicarlo y ver los cambios

Una vez que hayas guardado el archivo, Waybar no se enterará hasta que lo reinicies. Usa este comando en tu terminal para ver el nuevo "look":

```bash
killall waybar; waybar &

```

### 3. Ajustes rápidos que puedes hacer:

* **Transparencia:** Si quieres que la barra sea más o menos transparente, cambia el último número en `background-color: rgba(30, 30, 46, 0.5);`. El `0.5` es 50% transparente. `1.0` es opaco.
* **Espaciado:** Si sientes que los módulos están muy pegados a los bordes de la pantalla, puedes añadir un margen al `window#waybar` o cambiar el `margin: 4px 2px;` en los módulos.

### 4. ¿Faltan iconos?

Si en lugar de iconos ves **cuadrados con números o letras raras**, es porque te falta la fuente de iconos. Instálala así:

```bash
sudo pacman -S ttf-jetbrains-mono-nerd

```

**¿Te gustaría que añadamos un botón de "Power Menu" (Apagar/Reiniciar) al final de la barra para no tener que usar comandos?**
