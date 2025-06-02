¡Fantástico! Vamos a explorar **la sintaxis y reglas básicas de Lua** desde cero, con ejemplos claros y aplicaciones prácticas en Neovim. Al final, podrás escribir scripts sencillos y personalizar tu editor como un/a pro. 💻✨  

---

# 🔹 **Sintaxis Básica de Lua**  
Lua es minimalista pero poderoso. Aquí tienes sus fundamentos:  

---

## 1. **Comentarios**  
```lua
-- Esto es un comentario de una línea

--[[
  Esto es un comentario
  multilínea
]]
```

---

## 2. **Variables y Tipos de Datos**  
### **Tipos básicos**  
```lua
local numero = 42          -- Número
local texto = "Hola Lua"   -- String
local booleano = true      -- Booleano
local nulo = nil           -- Valor nulo
local tabla = {1, 2, 3}    -- Tabla (equivalente a arrays/objetos)
```

### **Reglas clave**  
- **`local`**: Define variables locales (si no usas `local`, son globales).  
- **Case-sensitive**: `variable` ≠ `Variable`.  
- **No tipado estático**: Las variables pueden cambiar de tipo.  

---

## 3. **Operadores**  
### **Aritméticos**  
```lua
local suma = 5 + 3   -- 8
local modulo = 10 % 3 -- 1 (resto de la división)
```

### **Comparación**  
```lua
5 == 5     -- true
5 ~= 3     -- true (~= es "no igual")
```

### **Concatenación**  
```lua
local saludo = "Hola " .. "Lua"  -- "Hola Lua"
```

---

## 4. **Estructuras de Control**  
### **Condicionales (`if`, `else`)**  
```lua
local edad = 18

if edad >= 18 then
  print("Mayor de edad")
else
  print("Menor de edad")
end
```

### **Bucles (`for`, `while`)**  
```lua
-- For numérico
for i = 1, 5 do
  print("Número: " .. i)
end

-- While
local contador = 0
while contador < 3 do
  print("Contador: " .. contador)
  contador = contador + 1
end
```

---

## 5. **Funciones**  
```lua
-- Definición
function saludar(nombre)
  return "¡Hola, " .. nombre .. "!"
end

-- Llamada
print(saludar("Neovim"))  -- ¡Hola, Neovim!
```

### **Funciones anónimas (útiles en Neovim)**  
```lua
local multiplicar = function(a, b)
  return a * b
end

print(multiplicar(3, 4))  -- 12
```

---

## 6. **Tablas (Arrays y Objetos)**  
### **Array (lista)**  
```lua
local frutas = {"manzana", "banana", "naranja"}
print(frutas[1])  -- "manzana" (¡Lua indexa desde 1!)
```

### **Tabla como diccionario**  
```lua
local persona = {
  nombre = "Ana",
  edad = 30
}

print(persona.nombre)  -- "Ana"
```

---

## 7. **Módulos y `require`**  
Lua usa `require` para importar módulos (ideal para organizar configs en Neovim).  

### **Ejemplo: Archivo `lua/mi_modulo.lua`**  
```lua
local M = {}

function M.saludar()
  print("Hola desde un módulo")
end

return M
```

### **Uso en `init.lua`**  
```lua
local mod = require('mi_modulo')
mod.saludar()  -- "Hola desde un módulo"
```

---

# 🔹 **Aplicación en Neovim**  
### **Ejemplo 1: Crear un comando personalizado**  
```lua
-- En tu init.lua
vim.api.nvim_create_user_command('Saludar', function()
  print("¡Hola desde Neovim con Lua!")
end, {})
```
- **Uso**: Ejecuta `:Saludar` en Neovim.  

### **Ejemplo 2: Mapear una función a un atajo**  
```lua
vim.keymap.set('n', '<Leader>s', function()
  local nombre = vim.fn.input("¿Tu nombre? ")
  print("¡Bienvenido, " .. nombre .. "!")
end)
```
- **Uso**: Presiona `<Espacio>s` (asumiendo que `<Leader>` es espacio).  

---

# 📌 **Reglas Importantes**  
1. **Índices comienzan en 1**, no en 0.  
2. **`nil`** es el valor "no existente" (similar a `null` en otros lenguajes).  
3. **Evita variables globales** (usa `local` siempre que puedas).  
4. **Las tablas son el único tipo de estructura de datos** (pero son ultra flexibles).  

---

# 🚀 **¿Qué sigue?**  
1. **Practica**: Crea funciones en Lua que interactúen con Neovim (ej: cambiar temas, manejar buffers).  
2. **Explora la API de Neovim**:  
   ```lua
   print(vim.inspect(vim.api.nvim_list_wins()))  -- Lista ventanas abiertas
   ```  
3. **Combina Lua y Shell**:  
   ```lua
   vim.api.nvim_create_user_command('Listar', function()
     print(vim.fn.system('ls -la'))
   end, {})
   ```  

---

### 🔥 **Reto Opcional**  
Crea un comando `:Clima` que muestre el clima actual usando `curl` y Lua:  
```lua
vim.api.nvim_create_user_command('Clima', function()
  local clima = vim.fn.system('curl wttr.in?format="%C+%t"')
  vim.notify("El clima: " .. clima, "info")
end, {})
```  
*(Requiere `curl` instalado)*.  

---

¡Con esto ya tienes los cimientos de Lua para Neovim! ¿Quieres profundizar en algún tema en particular? 😊
