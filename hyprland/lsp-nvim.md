# LSP no carga al abrir nvim desde Hyprland

## Problema

```conf
bind = $mainMod, u, exec, kitty --title tasks -e sh -c "nvim ./git/tareas/tasks.md"
```

Neovim no carga ningún servidor LSP (marksman, markdown_oxide, etc.).

## Causa

Hyprland ejecuta el comando desde `~` (home). El LSP necesita un root marker (`.git`, `tsconfig.json`, etc.) hacia arriba desde el archivo. Si `~/git` es un repo y el archivo está dentro, el LSP no encuentra `.git` porque parte de `~`.

## Solución

Cambiar el working directory antes de abrir nvim:

```conf
bind = $mainMod, u, exec, kitty --title tasks -e sh -c "cd ~/git && nvim tareas/tasks.md"
```

## Diagnóstico

Dentro de nvim:

```
:pwd                → cwd actual
:LspInfo            → si hay cliente adjunto
:echo vim.fs.find(".git", { upward = true })  → qué .git encuentra
```
