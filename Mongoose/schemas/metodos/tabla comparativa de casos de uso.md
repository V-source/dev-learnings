Perfecto ⚡ Aquí tienes una **tabla comparativa completa** entre los **métodos de documento (`schema.methods`)** y los **métodos estáticos (`schema.statics`)** en **Mongoose**, con explicación, ejemplos conceptuales y cuándo conviene usar cada uno 👇

---

### 🧾 **Tabla Comparativa — Métodos de Documento vs Métodos Estáticos**

| **Criterio**                         | **Métodos de Documento (`schema.methods`)**                                                                                                                 | **Métodos Estáticos (`schema.statics`)**                                    |
| ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| 🧱 **Nivel de aplicación**           | Se aplican **a una instancia específica** del documento.                                                                                                    | Se aplican **al modelo completo** (colección entera).                       |
| 🎯 **Propósito principal**           | Lógica que **depende de los datos de un documento concreto**.                                                                                               | Lógica que **involucra múltiples documentos** o consultas globales.         |
| 🧩 **Contexto de uso**               | Cuando ya tienes un documento cargado y necesitas operar sobre él.                                                                                          | Cuando necesitas **buscar, filtrar o analizar documentos** desde el modelo. |
| 🔍 **Acceso al modelo**              | Accede a los campos y métodos del documento (`this` se refiere al documento).                                                                               | Accede a los métodos del modelo (`this` se refiere al modelo Mongoose).     |
| 🧠 **Tipo de lógica común**          | Operaciones o validaciones personalizadas en un solo objeto.                                                                                                | Consultas, búsquedas, estadísticas, o inicialización de datos.              |
| ⚙️ **Ejemplo conceptual (Usuarios)** | `user.verifyPassword(password)` → compara contraseñas.                                                                                                      | `User.findByEmail(email)` → busca usuario por correo.                       |
| 💬 **Ejemplo conceptual (Pedidos)**  | `order.calculateTotal()` → suma los precios del pedido actual.                                                                                              | `Order.findPending()` → retorna todos los pedidos pendientes.               |
| 📦 **Entrada de datos**              | No requiere parámetros complejos: usa directamente las propiedades del documento.                                                                           | Requiere parámetros para definir la búsqueda o acción.                      |
| 🔐 **Uso típico en autenticación**   | Verificar token, comparar contraseñas, generar JWT por usuario.                                                                                             | Buscar usuario por email, validar existencia o crear si no existe.          |
| 🧮 **Uso típico en e-commerce**      | Calcular total de carrito, aplicar descuentos, validar stock del pedido.                                                                                    | Obtener ventas totales, productos más vendidos, estadísticas.               |
| 🔄 **Uso típico en apps sociales**   | Comprobar si el usuario sigue a otro, calcular número de likes.                                                                                             | Obtener los usuarios más activos o las publicaciones recientes.             |
| 🛠️ **Ventaja principal**            | Mantiene la lógica de negocio encapsulada por documento.                                                                                                    | Permite crear “métodos utilitarios” reutilizables a nivel de colección.     |
| ⚡ **Desventaja principal**           | No puede acceder a otros documentos directamente (fuera de su instancia).                                                                                   | No puede acceder a campos individuales de un documento sin cargarlo.        |
| 📘 **Analogía en programación**      | Similar a **métodos de instancia** de una clase.                                                                                                            | Similar a **métodos estáticos** de una clase.                               |
| 🧠 **Ejemplo conceptual (Blog)**     | `post.publish()` → cambia el estado del post a publicado.                                                                                                   | `Post.findByAuthor(authorId)` → devuelve todos los posts del autor.         |
| 🧰 **Ideal cuando...**               | Trabajas con **un documento ya cargado** (ej. `user.save()` después de modificarlo).                                                                        | Necesitas **obtener o procesar muchos documentos** a la vez.                |
| 🔄 **Ejemplo de combinación**        | Un método estático busca el documento (`User.findByEmail(email)`), y luego el método de documento verifica la contraseña (`user.verifyPassword(password)`). |                                                                             |
| 💡 **Uso complementario**            | Se suelen usar **juntos**: el estático busca, el documento actúa.                                                                                           | Se suele usar **primero**, para localizar el documento.                     |

---

### 🧠 **Resumen práctico**

| Situación                                                                                                                 | Qué usar                                                                     |
| ------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| Necesitas operar sobre **una instancia específica** ya existente (por ejemplo, un usuario autenticado, una orden cargada) | **Método de documento**                                                      |
| Necesitas **buscar, crear, filtrar o procesar datos** desde el modelo global                                              | **Método estático**                                                          |
| Quieres mantener la **lógica encapsulada en el propio modelo**, evitando funciones externas o duplicadas                  | Ambos (depende del alcance de la lógica)                                     |
| Deseas aplicar **patrones OOP (orientado a objetos)** sobre tus modelos Mongoose                                          | Ambos: estáticos para la “clase”, métodos de documento para las “instancias” |

---

¿Quieres que te dé una **versión con ejemplos de código cortos y explicados** para cada tipo (uno de usuario, uno de pedido y uno de blog)?
Te ayudaría a ver cómo se combinan ambos en un flujo real.
