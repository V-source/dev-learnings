# Stow Tips

GNU Stow es un gestor de archivos de configuración (dotfiles) que usa symlinks para mantener tus configs organizadas y sincronizadas entre máquinas.

## Estructura de esta Guía

1. **[Setup Inicial](01-setup.md)** - Cómo crear tu repositorio de dotfiles
2. **[Comandos Básicos](02-comandos.md)** - Los comandos esenciales de stow
3. **[Flujo de Trabajo](03-flujo-trabajo.md)** - Cómo trabajar día a día
4. **[Configuración Avanzada](04-stowrc.md)** - El archivo .stowrc y opciones avanzadas

## Requisitos

```bash
# Instalar stow en tu distro
# Arch Linux
sudo pacman -S stow

# Debian/Ubuntu
sudo apt install stow

# macOS
brew install stow
```

## Concepto Básico

```
~/dotfiles/           ← Tu repositorio (stow directory)
├── vim/
│   └── .vimrc       ← Cuando stowees, crea ~/.vimrc → symlink
├── zsh/
│   └── .zshrc
└── git/
    └── .gitconfig

~ (home)             ← Target directory
├── .vimrc → ../dotfiles/vim/.vimrc
├── .zshrc → ../dotfiles/zsh/.zshrc
└── .gitconfig → ../dotfiles/git/.gitconfig
```