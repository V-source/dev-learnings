error: 
```bash

→ pnpm

bash: /home/villegas/.local/share/pnpm/bin/pnpm: No such file or directory

→ 
```
El problema ahora es que tu shell (bash) tiene memorizada (en caché) la ruta vieja del binario de pnpm que acabamos de borrar (/home/villegas/.local/share/pnpm/bin/pnpm), pero al instalarlo globalmente con npm install -g, Node lo ha colocado en la ruta global estándar de npm.

Como bash busca en su historial interno antes de revisar el $PATH real, se confunde.


solucion:

Limpia la caché de comandos de Bash
Ejecuta este comando para forzar a bash a olvidar las rutas viejas y escanear el $PATH de nuevo:
```bash
hash -r
```


---

error:
```bash
→ pnpm
/home/villegas/.local/share/pnpm/bin/../global/v11/1561-19effdde108/node_modules/@pnpm/exe/pnpm: line 1: This: command not found
```

Este error ocurre porque el ejecutable binario de `pnpm` en tu sistema se ha corrompido. El mensaje `line 1: This: command not found` suele significar que el archivo binario fue sobrescrito por un texto plano (probablemente un archivo HTML o un mensaje de error que empieza con *"This..."* descargado por accidente durante una instalación o actualización fallida).

Dado que usas **Arch Linux**, la forma más limpia y definitiva de solucionar esto en tu entorno es reinstalar `pnpm` asegurando que tus variables de entorno queden bien configuradas.

Sigue estos pasos para repararlo:

### 1. Limpiar el binario roto

Primero, elimina el binario corrupto que está causando el conflicto en tu directorio local:

```bash
rm -rf /home/villegas/.local/share/pnpm

```

### 2. Reinstalar pnpm de forma limpia

La forma oficial y más aislada de instalarlo sin depender de que Node/NPM ya estén funcionando es mediante su script nativo de shell. Ejecuta:

```bash
curl -fsSL https://get.pnpm.io/install.sh | sh -

```

### 3. Verificar tus variables de entorno (`.bashrc` o `.zshrc`)

El instalador agregará automáticamente la ruta de `pnpm` a tu archivo de configuración de la shell. Asegúrate de recargar tu terminal para aplicar los cambios:

```bash
source ~/.bashrc
# O si usas zsh: source ~/.zshrc

```

*(Opcional)* Si por alguna razón sigues teniendo problemas de "Command not found", abre tu archivo de configuración (`~/.bashrc`, `~/.zshrc` o el que manejes en tu setup) y verifica que existan estas líneas al final:

```bash
# pnpm
export PNPM_HOME="/home/villegas/.local/share/pnpm"
case ":$PATH:" in
  *":$PNPM_HOME:"*) ;;
  *) export PATH="$PNPM_HOME:$PATH" ;;
esac
# pnpm end

```

### 4. Alternativa desde los repositorios de Arch (Recomendado)

Si prefieres que el gestor de paquetes de Arch Linux maneje las actualizaciones de `pnpm` por ti de manera global, puedes instalarlo directamente desde los repositorios oficiales con Pacman. Esto evitará problemas con scripts locales en el home:

```bash
sudo pacman -S pnpm

```

Una vez terminado cualquiera de los dos métodos, ejecuta `pnpm -v` para confirmar que vuelve a responder correctamente.
