# Comandos Básicos

## Stow/unstow Manual

### Stow un paquete (crear symlinks)

```bash
cd ~/dotfiles
stow vim
```

### Stow múltiples paquetes

```bash
stow vim zsh git tmux
```

### Unstow un paquete (eliminar symlinks)

```bash
stow -D vim
```

### Unstow todos los paquetes

```bash
stow -D *
```

## Opciones Útiles

### Especificar directorios

```bash
# -d: directorio de stow (donde están los paquetes)
# -t: target (donde crear los symlinks, típicamente ~)
stow -d ~/dotfiles -t ~ vim
```

### Variables de entorno

```bash
export STOW_DIR=~/dotfiles
stow vim zsh  # usa STOW_DIR como -d
```

### Verbose y dry-run

```bash
stow -nv vim       # -n: dry-run, -v: verbose
stow -nvvim        # combinada
```

### Restow (recargar symlinks)

```bash
stow -R vim        # reinstala el paquete
stow -R            # restow todos
```

Útil cuando:
- Añades archivos a un paquete
- Eliminas archivos de un paquete
- Actualizas tu configuración

## Dotfiles (archivos que empiezan con .)

Por defecto stow ignora archivos que empiezan con `.` en el source. Para que los procese:

```bash
stow --dotfiles vim
```

O configura esto en `.stowrc` (ver siguiente guía).

## Ignorar Archivos

```bash
stow --ignore='README.*' --ignore='\.git.*' vim
```

## Verificar Estado

```bash
stow --check vim  # verifica si los symlinks están correctos
```

Si retorna 0, todo está bien. Si retorna 1, hay problemas.

## Comandos Shortcuts

| Comando | Descripción |
|---------|-------------|
| `stow vim` | Instalar vim |
| `stow -D vim` | Desinstalar vim |
| `stow -R vim` | Reinstalar vim |
| `stow -n vim` | Dry-run vim |
| `stow -nv vim` | Verbose dry-run |