# nvim-cmp — Autocompletions personalizados

## Arquitectura

nvim-cmp funciona con **sources**. Cada source provee items de autocompletado para un contexto específico.

Sources básicos:
- `nvim_lsp` → del LSP (variables, funciones, tipos)
- `luasnip` → snippets
- `buffer` → palabras del buffer actual
- `path` → rutas de archivos
- `md_tags` → custom (ejemplo de este doc)

---

## Crear un custom source

Se registra con `cmp.register_source()` y se define la función `complete`:

```lua
cmp.register_source("mi_source", {
  complete = function(self, request, callback)
    -- request.context.cursor_before_line → texto antes del cursor
    -- callback({ items = {...} }) → devuelve sugerencias
  end,
})
```

### Estructura de un item

```lua
{
  label = "@todo",       -- texto mostrado
  kind = 14,             -- tipo (14 = keyword, 1 = text, 9 = snippet, etc.)
  insertText = "@todo",  -- texto insertado (opcional, default = label)
  detail = "descripción", -- texto secundario gris
  documentation = "ayuda", -- texto en ventana flotante
}
```

[Lista completa de kinds](https://microsoft.github.io/language-server-protocol/specifications/lsp/3.17/specification/#textDocument_completion)

---

### Ejemplo real: tags `@` para markdown

**Source**:

```lua
cmp.register_source("md_tags", {
  complete = function(self, request, callback)
    if vim.bo.filetype ~= "markdown" then
      callback()
      return
    end
    local before = request.context.cursor_before_line:match("@([%w_]*)$")
    if not before then
      callback()
      return
    end
    local tags = { "todo", "fix", "bug", "idea", "goal", "note", "warn",
                   "info", "done", "onw", "desc", "test", "pending",
                   "git", "tasks", "code", "snippet" }
    local items = {}
    for _, tag in ipairs(tags) do
      if tag:find(before, 1, true) == 1 then
        table.insert(items, { label = "@" .. tag, kind = 14 })
      end
    end
    callback({ items = items })
  end,
})
```

**Registro en sources** (con trigger_characters):

```lua
sources = cmp.config.sources({
  { name = "md_tags", trigger_characters = { "@" } },
  { name = "nvim_lsp" },
  { name = "luasnip" },
  { name = "path" },
}, {
  { name = "buffer" },
})
```

**Clave**: `trigger_characters = { "@" }` hace que el source se dispare automáticamente al tipear `@`, sin necesidad de presionar `<C-\\>`.

---

## Tips avanzados

### Filtrar por filetype

```lua
complete = function(self, request, callback)
  if vim.bo.filetype ~= "markdown" and vim.bo.filetype ~= "tex" then
    callback()
    return
  end
  -- ...
end,
```

### Items con documentación

```lua
table.insert(items, {
  label = "@fix",
  kind = 14,
  documentation = "Marca algo que necesita reparación",
})
```

### Usar la línea completa para decidir

```lua
local line = request.context.cursor_before_line
if line:match("^%s*%- %[ %] @") then
  -- Estamos en una checklist, sugerir tags específicos
end
```

### Priorizar tu source sobre otros

El orden en la tabla `sources` define la prioridad. El primero aparece más arriba.

```lua
sources = cmp.config.sources({
  { name = "md_tags", priority = 100, trigger_characters = { "@" } },
  { name = "nvim_lsp" },
  ...
})
```

---

## Debugging de sources

Si un source no aparece:

```vim
:lua print(vim.inspect(require("cmp").get_sources()))
```

Para ver qué recibe el source:

```lua
complete = function(self, request, callback)
  print(vim.inspect(request.context))
  -- ...
end,
```

---

## Snippets vs Custom Sources

| Snippet | Custom Source |
|---------|--------------|
| Se expande con trigger completo (`@todo` + Tab) | Sugiere mientras tipeás |
| Útil para bloques grandes | Útil para palabras dinámicas |
| Menos intrusivo | Más inmediato |

Combinados son poderosos: usá snippets para plantillas (frontmatter, callouts, tablas) y custom sources para palabras clave cortas (`@tags`, `:emoji:`, `#hashtags`).
