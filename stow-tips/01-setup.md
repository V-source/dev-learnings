# Setup Inicial

## 1. Crear el Repositorio

```bash
mkdir -p ~/dotfiles
cd ~/dotfiles
git init
```

## 2. Crear Paquetes

Crea un directorio por cada aplicación/config que quieras gestionar:

```bash
mkdir -p ~/dotfiles/{vim,zsh,git,tmux,vscode,alacritty}
```

## 3. Importar Configs Existentes

### Opción A: Manual (recomendado para control total)

```bash
# Mover archivos manualmente a su paquete
mv ~/.vimrc ~/dotfiles/vim/.vimrc
mv ~/.vim ~/dotfiles/vim/.vim
mv ~/.zshrc ~/dotfiles/zsh/.zshrc
mv ~/.gitconfig ~/dotfiles/git/.gitconfig
mv ~/.tmux.conf ~/dotfiles/tmux/.tmux.conf
```

### Opción B: Automático con `--adopt`

Si ya creaste los directorios vacíos:

```bash
cd ~/dotfiles
stow --adopt vim zsh git tmux
```

Esto mueve automáticamente tus configs existentes a los paquetes de stow.

## 4. Estructura de Paquetes

Cada paquete debe reflejar la estructura del home:

```
dotfiles/
├── vim/
│   ├── .vimrc
│   └── .vim/
│       └── init.vim
├── zsh/
│   └── .zshrc
└── git/
    └── .gitconfig
```

**Importante:** El archivo `.vimrc` dentro del paquete `vim/` se linkará como `~/.vimrc`.

## 5. Primera Configuración Git

```bash
cd ~/dotfiles
git add -A
git commit -m "Initial dotfiles setup"
git remote add origin git@github.com:tu-usuario/dotfiles.git
git push -u origin main
```

## Tips

- Mantén nombres simples: `vim`, `zsh`, `git`, no `vim-config` o `my-vim-settings`
- No incluyas archivos sensibles (passwords, tokens) - usa `.gitignore`
- El directorio `dotfiles` puede estar en cualquier lugar, commonly `~/dotfiles` o `~/.dotfiles`