# Configuración Avanzada: .stowrc

El archivo `.stowrc` te permite definir opciones por defecto para no tener que especificarlas cada vez.

## Crear .stowrc

```bash
cat > ~/dotfiles/.stowrc << 'EOF'
--dir=~/dotfiles
--target=~
--dotfiles
--ignore='README.*'
--ignore='\.git'
--ignore='\.gitignore'
EOF
```

## Opciones Explicadas

| Opción | Descripción |
|--------|-------------|
| `--dir=~/dotfiles` | Directorio donde están los paquetes |
| `--target=~` | Target (home directory) |
| `--dotfiles` | Procesar archivos que empiezan con `.` |
| `--ignore='PATRON'` | Ignorar archivos que coincidan |

## Ignores Comunes

```bash
--ignore='README.*'
--ignore='LICENSE'
--ignore='\.git'
--ignore='\.gitignore'
--ignore='\.stowrc'
--ignore='\.DS_Store'
```

## Casos de Uso

### 1. Stowear todo sin especificar paquetes

Con `.stowrc` configurado:

```bash
cd ~/dotfiles
stow */        # stowea todos los directorios
```

### 2. Stowear todos los paquetes con un solo comando

En tu script de instalación:

```bash
#!/bin/bash
cd ~/dotfiles
for dir in */; do
    stow "$(basename "$dir")"
done
```

O simplemente:

```bash
stow */
```

### 3. Múltiples Stow Directories

Puedes tener varios directorios de stow en el mismo target usando `.stow` marker files:

```
~/dotfiles/.stow        ← marker vacío
~/work/stow/.stow       ← otro marker
```

Cada uno creará sus symlinks independientemente.

## Configuración por Paquete

Si necesitas opciones específicas por paquete, puedes usar múltiples archivos `.stowrc`:

```bash
# En el directorio de cada paquete
~/dotfiles/vim/.stowrc
~/dotfiles/zsh/.stowrc
```

## Tips

- El `.stowrc` debe estar en el directorio donde ejecutas `stow`
- Las opciones de `.stowrc` se aplican antes de los argumentos de CLI
- Puedes usar `--verbose` y `--dry-run` en `.stowrc` para debugging

## Ejemplo Completo

```bash
# ~/dotfiles/.stowrc
--dir=/home/usuario/dotfiles
--target=/home/usuario
--dotfiles
--ignore=README.md
--ignore=LICENSE
--ignore=\.git
--ignore=\.gitignore
--ignore=\.DS_Store
--ignore=scripts/
```

Con esto, simplemente ejecutas:

```bash
cd ~/dotfiles
stow */   # stowea todo
```