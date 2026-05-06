# Flujo de Trabajo

## Día a Día

### Editar una configuración

直接 edita el archivo en tu home (ej: `~/.vimrc`) o en el paquete (`~/dotfiles/vim/.vimrc`). Ambos funcionan porque son el mismo archivo (symlink).

```bash
# Opción 1: Editar en home (recomendado para cambios rápidos)
nvim ~/.vimrc

# Opción 2: Editar en el repo
nvim ~/dotfiles/vim/.vimrc
```

### Sincronizar cambios

```bash
cd ~/dotfiles
git add -A
git commit -m "Update vim config"
git push
```

### En otra máquina

```bash
# Pull cambios
cd ~/dotfiles
git pull

# Reinstalar el paquete actualizado
stow -R vim
```

## Agregar un Nuevo Paquete

1. Crear directorio:
   ```bash
   mkdir -p ~/dotfiles/nuevo
   ```

2. Mover/configurar archivos:
   ```bash
   mv ~/.nuevoconfig ~/dotfiles/nuevo/.nuevoconfig
   ```

3. Stowear:
   ```bash
   cd ~/dotfiles
   stow nuevo
   ```

4. Commitear:
   ```bash
   git add nuevo && git commit -m "Add nuevo config"
   ```

## Eliminar un Paquete

1. Unstow:
   ```bash
   stow -D nombrepaquete
   ```

2. Eliminar directorio del repo:
   ```bash
   rm -rf ~/dotfiles/nombrepaquete
   ```

3. Commitear:
   ```bash
   git add -A && git commit -m "Remove nombrepaquete"
   ```

## Nueva Instalación (desde cero)

```bash
# 1. Clonar tu repo
git clone git@github.com:tu-usuario/dotfiles.git ~/dotfiles

# 2. Stowear todo
cd ~/dotfiles
stow vim zsh git tmux

# O si tienes .stowrc configurado:
stow */
```

## Scripts Útiles

### Alias comunes (agrega a tu .zshrc/.bashrc)

```bash
alias ds="cd ~/dotfiles"
alias dstow="cd ~/dotfiles && stow */"
alias drestow="cd ~/dotfiles && stow -R */"
```

### Script de instalación rápido

```bash
#!/bin/bash
# install.sh

git clone git@github.com:tu-usuario/dotfiles.git ~/dotfiles

cd ~/dotfiles

# Si tienes .stowrc
stow */

# Si no, especifica cada paquete
stow vim zsh git tmux
```

## Recomendaciones

- **Edita en `~`**: Es más intuitivo. Los cambios se reflejan automáticamente en el repo.
- **Commit frecuentemente**: Cambios pequeños son más fáciles de trackear.
- **Restaura antes de editar**: Si modificas mucho, haz `stow -R` antes para asegurar estado limpio.
- **Usa branches**: Para cambios experimentales antes de hacer merge a main.