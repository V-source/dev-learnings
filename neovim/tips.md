# Neovim Tips

## matchadd en Neovim 0.12

La sintaxis cambió. En 0.12, `matchadd` tiene **5 parámetros**:

```lua
vim.fn.matchadd(group, pattern, priority, id, opts)
```

| Parámetro | Tipo | Descripción |
|-----------|------|-------------|
| `group` | string | Grupo de highlight |
| `pattern` | string | Patrón regex (Vim regex) |
| `priority` | number | Prioridad (default 10) |
| `id` | number | ID del match, -1 para autogenerar |
| `opts` | table | Opciones: `{ buffer = bufnr }` para buffer-local |

### Correcto (buffer-local)

```lua
vim.fn.matchadd("mdTag", "@\\w\\+", 10, -1, { buffer = 0 })
```

### Errores comunes

| Código | Error | Razón |
|--------|-------|-------|
| `matchadd(g, p, 10, { buffer = 0 })` | `E728: Using a Dictionary as a Number` | El 4to parámetro es `id` (número), no `opts`. Las opciones van en 5to. |
| `matchadd(g, p, 10, { buffer = 0 })` sin prioridad | Mismo error | Misma causa |

### Sin buffer-local (global, funciona igual)

```lua
vim.fn.matchadd("mdTag", "@\\w\\+")
```

Solo no usar `{ buffer = 0 }` y no hay error. El match se aplica a todos los buffers.

---

## Custom sources en nvim-cmp

Ver `nvim-cmp/autocompletions-personalizados.md`.

---

## Tips de @tags

### Highlight en todos los filetypes

En `config.lua`:

```lua
vim.cmd("highlight mdTag guifg=#f0c060 gui=bold")
vim.api.nvim_create_autocmd("FileType", {
  pattern = "*",
  callback = function()
    vim.fn.matchadd("mdTag", "@\\w\\+")
  end,
})
```

### Autocompletion en markdown

En `completions.lua`, dentro del `config` de nvim-cmp, antes de `cmp.setup()`:

```lua
cmp.register_source("md_tags", {
  complete = function(self, request, callback)
    if vim.bo.filetype ~= "markdown" then callback(); return end
    local before = request.context.cursor_before_line:match("@([%w_]*)$")
    if not before then callback(); return end
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

Y agregar en `sources`:

```lua
sources = cmp.config.sources({
  { name = "md_tags", trigger_characters = { "@" } },
  ...
})
```
