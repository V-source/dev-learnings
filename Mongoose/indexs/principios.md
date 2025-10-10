# de que tratan los index en mongoose?

`PermissionSchema.index({ module: 1, action: 1 }, { unique: true });`

Excelente pregunta 👌
Vamos paso a paso para que lo entiendas **claramente**:

---

### 🧩 ¿Qué son los *índices* en Mongoose (y MongoDB)?

Un **índice** es una estructura que **acelera las búsquedas** dentro de una colección.
Sin un índice, MongoDB tiene que **revisar todos los documentos** (lo que se llama un *collection scan*) cada vez que haces una consulta.

👉 Con un índice, MongoDB crea una especie de “tabla ordenada” con los valores de los campos que especifiques, lo que permite encontrar coincidencias **mucho más rápido**.

---

### 📘 Ejemplo base:

```js
PermissionSchema.index({ module: 1, action: 1 }, { unique: true });
```

Esto hace **dos cosas principales**:

---

#### 1️⃣ Crea un **índice compuesto**

El objeto `{ module: 1, action: 1 }` indica que el índice se compone de **dos campos:**

* `module`
* `action`

El `1` significa **orden ascendente** (podrías usar `-1` para descendente, aunque en la práctica no suele importar para índices únicos).

🔹 Ejemplo visual:
MongoDB ordena internamente tus documentos más o menos así:

| module | action |
| :----: | :----: |
|  users |  read  |
|  users |  write |
|  posts |  read  |
|  posts |  write |

Así, si haces una búsqueda como `{ module: 'users', action: 'read' }`, MongoDB no tiene que recorrer toda la colección: **salta directo al registro correcto**.

---

#### 2️⃣ Impone una **restricción de unicidad**

El segundo parámetro `{ unique: true }` le dice a MongoDB:

> “No permitas que existan dos documentos con la misma combinación de `module` y `action`.”

Eso significa que:

```js
{ module: "users", action: "read" }
```

✅ Se puede guardar una vez, pero
❌ Si intentas guardar **otro documento igual**, MongoDB lanzará un error `E11000 duplicate key`.

---

### ⚙️ En contexto práctico

Supón que tu colección define permisos del sistema.
Cada permiso combina un módulo y una acción:

| module | action | descripción           |
| :----- | :----- | :-------------------- |
| users  | read   | Ver lista de usuarios |
| users  | write  | Editar usuarios       |
| posts  | read   | Ver publicaciones     |

Con el índice:

```js
PermissionSchema.index({ module: 1, action: 1 }, { unique: true });
```

➡️ te aseguras de que **no haya duplicados** como:

```js
{ module: "users", action: "read" } // repetido
```

Y además, las consultas que combinan esos dos campos serán **mucho más rápidas**.

---

### 🧠 En resumen:

| Concepto                   | Significado                                            |
| -------------------------- | ------------------------------------------------------ |
| `index()`                  | Crea un índice sobre uno o más campos.                 |
| `{ module: 1, action: 1 }` | Define los campos indexados (en orden ascendente).     |
| `{ unique: true }`         | Garantiza que esa combinación de valores no se repita. |
| Beneficio                  | Mejora el rendimiento y asegura integridad de datos.   |

---

¿Quieres que te muestre cómo MongoDB crea ese índice internamente (por ejemplo, con `db.collection.getIndexes()` o cómo lo usa en una consulta con `explain()`)?
Eso te ayudaría a ver **qué tan útil es realmente**.
