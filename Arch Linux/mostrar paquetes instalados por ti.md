En Arch Linux y sus derivados (como Manjaro), puedes usar varios comandos para listar los paquetes instalados manualmente (no como dependencias). Aquí tienes las opciones más útiles:

### 1. **Listar TODOS los paquetes explícitamente instalados** (los que tú instalaste directamente):
```bash
pacman -Qqe
```
- `-Q`: Query (consultar paquetes instalados)
- `-q`: Quiet (solo nombres, sin versión)
- `-e`: Explicit (solo paquetes instalados manualmente)

### 2. **Listar paquetes explícitamente instalados con más detalles** (nombre + versión):
```bash
pacman -Qe
```

### 3. **Listar paquetes instalados por un usuario específico** (útil si usas AUR helpers como yay):
```bash
pacman -Qq --foreign
```
Esto muestra paquetes que no están en los repositorios oficiales (generalmente instalados desde AUR).

### 4. **Para diferenciar entre paquetes oficiales y AUR**:
```bash
# Paquetes oficiales instalados manualmente
pacman -Qqen

# Paquetes de AUR instalados manualmente
pacman -Qqem
```

### 5. **Si usas yay (AUR helper)** y quieres ver solo los paquetes de AUR que instalaste:
```bash
yay -Qqem
```

### 6. **Para guardar la lista en un archivo** (útil para backups o reinstalación):
```bash
pacman -Qqe > paquetes-instalados.txt
```

### ¿Por qué es útil?
- **`-e`** → Muestra solo lo que instalaste activamente (no dependencias automáticas).
- **`-n`** → Filtra paquetes de repositorios oficiales.
- **`-m`** → Filtra paquetes instalados manualmente desde AUR.

### Extra:
Si quieres ver las dependencias instaladas automáticamente (para limpiarlas si ya no son necesarias):
```bash
pacman -Qqd
```
