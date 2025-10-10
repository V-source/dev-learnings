
## Resumen sección por sección de la guía de Mongoose

### Introducción / Getting Started

* Mongoose es una herramienta de **modelado de objetos** (ODM) para MongoDB en entornos asíncronos como Node.js.
* Primero se instala con `npm install mongoose`.
* Se conecta a una base de datos MongoDB mediante `mongoose.connect(...)`.
* Tras conectar, puedes definir **esquemas (Schemas)** que describen la estructura de los documentos de una colección.
* A partir del esquema, creas un **modelo (Model)** que te permite interactuar con la colección (crear, leer, actualizar, borrar documentos).
* Se pueden definir métodos de instancia (methods) en los documentos, atributos estáticos (statics) en el modelo, etc.

---

### Schemas (Esquemas)

* Todo empieza con un **Schema**. Un esquema mapea a una colección de MongoDB y define la forma de los documentos en esa colección. ([mongoosejs.com][1])
* Los campos del esquema se definen con tipos (String, Number, Date, Boolean, Array, Map, etc.). ([mongoosejs.com][1])
* Puedes usar una sintaxis corta cuando solo necesitas indicar el tipo (por ejemplo, `title: String`) o una definición más completa con opciones.
* Se admiten campos anidados (objetos dentro de objetos). Pero ojo: los objetos intermedios que no tienen una propiedad `type` no pueden tener validaciones por sí mismos; las validaciones se aplican en las “hojas” del árbol.
* Los esquemas también permiten definir:

  * **Métodos de instancia** (instance methods): funciones que operan sobre cada documento.
  * **Métodos estáticos** (statics): funciones definidas para el modelo en general.
  * **Helpers de consulta** (query helpers).
  * **Índices**, **virtuals** (propiedades virtuales), **aliases**, opciones del esquema, etc.

---

### Creating a Model (Crear un modelo)

* Una vez tienes el esquema, lo “compilas” en un modelo usando `mongoose.model('Nombre', schema)`.
* El modelo resultante te permite crear instancias (documentos) que respetan ese esquema y luego persistirlos en la base de datos.
* Mongoose agrega automáticamente un campo `_id` para cada documento, de tipo `ObjectId`, a menos que lo configures de otra manera.
* Los métodos de instancia y estáticos definidos en el esquema pasan a formar parte del prototipo del modelo/documento.

---

### Ids (Identificadores)

* Por defecto, cada documento tiene un campo `_id` que Mongoose genera automáticamente como `ObjectId`.
* Puedes acceder y usar ese campo para búsquedas, relaciones, etc.

---

### Instance Methods, Statics, Query Helpers (Métodos, Estáticos, Helpers de Consulta)

* **Instance Methods (Métodos de instancia)**: métodos que puedes llamar en cada documento, como `doc.customMethod()`.
* **Statics (Métodos estáticos)**: métodos definidos en el modelo, no en cada documento individual. Por ejemplo, `Model.findBySomething(...)`.
* **Query Helpers**: funciones que puedes encadenar en consultas para reutilizar fragmentos comunes de consulta.
* Estas tres capacidades ayudan a organizar la lógica del modelo (negocio, filtros, etc.) dentro del esquema de Mongoose.

---

### Indexes, Virtuals, Aliases, Options

* **Índices**: puedes definir índices directamente en el esquema, lo cual ayuda con el rendimiento de las consultas.
* **Virtuals**: propiedades calculadas que no se guardan en la base de datos, pero se exponen al leer documentos (por ejemplo, un campo completo “nombreCompleto” que concatena “nombre” + “apellido”).
* **Aliases**: alias para propiedades, de modo que puedas referirte a un campo con otro nombre desde el código.
* **Opciones del esquema**: al crear un esquema puedes pasar un objeto con opciones como `timestamps` (para que Mongoose agregue y mantenga `createdAt` / `updatedAt` automáticamente), `versionKey`, etc.

---

### Subdocuments (Subdocumentos)

* Se refiere a documentos anidados dentro de otros documentos (embebidos).
* Los subdocumentos pueden tener su propio esquema, validaciones y métodos.
* Son útiles para relaciones “uno a muchos” que no deseas modelar con una colección separada, cuando la cantidad de datos anidados no es muy grande.

---

### Queries (Consultas)

* Mongoose permite construir consultas (find, findOne, delete, update, etc.).
* Puedes usar funciones como `.where()`, `.equals()`, `.select()`, `.limit()`, `.skip()`, etc., para construir consultas más complejas.
* También puedes definir “query helpers” dentro del esquema para reutilizar fragmentos de consulta comunes.

---

### Validation (Validación)

* Mongoose ofrece mecanismos de validación integrados (por ejemplo, validadores para tipos, valores mínimos/máximos, expresiones regulares, validadores custom).
* La validación se ejecuta antes de guardar el documento (al llamar `save()`).
* Si una validación falla, el documento no se guarda y se devuelve un error con los detalles.

---

### Middleware (Hooks)

* Mongoose permite ejecutar funciones “antes” o “después” de ciertos eventos del documento/modelo, como `save`, `remove`, `update`, `validate`, etc.
* Esto es útil para lógica que debe ejecutarse automáticamente (por ejemplo, hashing de contraseña antes de guardar, limpieza de datos después de eliminar, etc.).
* Se pueden definir middleware a nivel de esquema.

---

### Populate (Poblado)

* El método `populate()` sirve para reemplazar referencias con documentos completos: en vez de tener solo un `ObjectId` que referencia otro documento, puedes cargar automáticamente el documento referenciado.
* Muy útil para relaciones entre colecciones.

---

### Discriminators

* Los discriminators permiten modelar clases “hijas” de un modelo base. Es decir, tener documentos que comparten algunas propiedades comunes, pero con campos adicionales específicos según “tipo”.
* Utilidades para herencia de esquemas.

---

### Plugins

* Los plugins permiten extender la funcionalidad de Mongoose (por ejemplo, agregar validación, transformación, métodos adicionales) de forma reutilizable.
* Puedes aplicar plugins sobre un esquema, y ese plugin inyecta lógica adicional.

---

### Timestamps

* La opción `timestamps: true` permite que Mongoose maneje automáticamente dos campos: `createdAt` y `updatedAt` en el esquema, actualizándolos cuando corresponda.

---

### Transactions (Transacciones)

* Permite agrupar múltiples operaciones en una transacción atómica, de modo que todas se ejecuten o ninguna.
* Útil cuando necesitas garantizar consistencia cuando haces varias operaciones relacionadas en diferentes colecciones o documentos.

---

### TypeScript

* La guía también ofrece pautas y ejemplos para usar Mongoose de forma efectiva con TypeScript, definiendo interfaces/decortes para esquemas, modelos y documentos.

---

### API Reference / Apéndices

* Además de la guía de usuario, Mongoose provee una referencia de API con detalles de clases como `Schema`, `Model`, `Document`, `Query`, agregaciones, tipos de esquema, virtuals, etc.
* También incluye guías de migración entre versiones mayores, compatibilidad entre versiones y preguntas frecuentes.

---

Si quieres, puedo darte **la sección “Methods” completamente traducida al español**, con ejemplos intactos, para que la estudies con más detalle. ¿Te gustaría eso ahora?

[1]: https://mongoosejs.com/docs/guide.html?utm_source=chatgpt.com "Mongoose v8.19.1: Schemas"
