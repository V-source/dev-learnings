
### **1. Instalar Flutter en Arch Linux**  
#### **Opción recomendada (usando `yay` para AUR)**:

```bash
yay -S flutter
```
Esto instalará Flutter junto con sus dependencias (como el SDK de Dart).  

#### **Verificar la instalación**:
```bash
flutter --version
flutter doctor
```
Si `flutter doctor` muestra errores (como Android Studio no instalado), ignóralos por ahora (a menos que quieras desarrollar para Android/iOS).

---

### **2. Configurar Flutter en Neovim (LazyVim)**  
Para que Flutter y Dart funcionen bien en Neovim, necesitarás:  

#### **a) Plugins recomendados** (agrégalos en tu configuración de LazyVim: `~/.config/nvim/lua/plugins/`):  
```lua
return {
  -- Soporte para Dart/Flutter
  {
    "akinsho/flutter-tools.nvim",
    dependencies = { "nvim-lua/plenary.nvim" },
    config = function()
      require("flutter-tools").setup {
        dev_log = {
          enabled = true,
          open_cmd = "tabedit", -- abre los logs en una pestaña
        },
      }
    end,
  },

  -- Autocompletado (si no lo tienes ya)
  {
    "hrsh7th/nvim-cmp",
    dependencies = {
      "hrsh7th/cmp-nvim-lsp",
      "lukas-reineke/cmp-rg", -- opcional: búsqueda rápida
    },
  },
}
```
Ejecuta `:Lazy sync` después de guardar los cambios.

#### **b) LSP para Dart/Flutter**:
El plugin `flutter-tools.nvim` ya configura el LSP automáticamente, pero asegúrate de tener:  
```bash
yay -S dart  # Instala el SDK de Dart (si no lo hizo Flutter)
```

---

### **3. Comandos útiles en Neovim para Flutter**  
Con `flutter-tools.nvim`, tendrás acceso a:  
- `:FlutterRun` → Ejecuta la app en modo debug.  
- `:FlutterDevices` → Lista dispositivos conectados.  
- `:FlutterReload` → Recarga la app en caliente.  
- `:FlutterOutline` → Muestra el árbol de widgets.  

**Atajo recomendado** (añade en `~/.config/nvim/lua/config/keymaps.lua`):  
```lua
vim.keymap.set("n", "<leader>fr", ":FlutterRun<CR>", { desc = "Flutter Run" })
```

---

### **4. Solución de Problemas Comunes**  
#### **Error: "Flutter no está en PATH"**  
Añade esto a tu `~/.bashrc` o `~/.zshrc`:  
```bash
export PATH="$PATH:/opt/flutter/bin"
```
Luego ejecuta:  
```bash
source ~/.bashrc  # o source ~/.zshrc
```

#### **Android Studio (opcional, si desarrollas para móvil)**  
```bash
yay -S android-studio
flutter doctor --android-licenses  # Acepta las licencias
```

---

### **5. Bonus: Debugging en Neovim**  
Instala **Dart DevTools** para debugging visual:  
```bash
flutter pub global activate devtools
devtools  # Abre la interfaz web
```
Luego conéctalo desde Neovim con `:FlutterDevTools`.

---

### **Resumen**  
1. Instala Flutter desde AUR (`yay -S flutter`).  
2. Configura `flutter-tools.nvim` en LazyVim.  
3. Usa los comandos de Flutter directamente desde Neovim.  

¿Necesitas ajustar algo para tu workflow? Por ejemplo, si prefieres debuggear con **DAP** en vez de LSP, puedo guiarte con eso también. 😊  

*(Documentación oficial de [Flutter para Linux](https://docs.flutter.dev/get-started/install/linux) y [flutter-tools.nvim](https://github.com/akinsho/flutter-tools.nvim))*
