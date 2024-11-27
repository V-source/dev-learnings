# `PRE` Y `BCRYPTJS` PARA ENCRIPTAR CONTRASEÑA

```javascript

UsersSchema.pre("save", async function (next) {
  if (this.isModified("password")) {
    // Solo encriptamos la contraseña si ha sido modificada
    this.password = await bcrypt.hash(this.password, 10); // Encriptamos con bcrypt usando 10 rondas de sal
  }
  next(); // Continuamos con el guardado del documento
});
```

Este código es un **middleware de Mongoose** que se ejecuta antes de guardar un documento de usuario en la base de datos. Su propósito es **encriptar la contraseña** del usuario antes de almacenarla.

### EXPLICACIÓN PASO A PASO:

1. **`UsersSchema.pre("save", async function (next) { ... })`**:
   - Este es un middleware de Mongoose que se ejecuta antes de guardar un documento (`save`) de la colección `Users`.
   - Se usa la palabra clave `pre` para indicar que esta función se ejecutará antes de guardar el documento.

2. **`if (this.isModified("password")) { ... }`**:
   - Aquí se verifica si el campo `password` ha sido modificado (es decir, si el usuario ha cambiado su contraseña).
   - Si el campo `password` no ha cambiado, no se realiza ninguna acción de encriptación.

3. **`this.password = await bcrypt.hash(this.password, 10);`**:
   - Si la contraseña ha sido modificada, se encripta usando **bcrypt**.
   - La función `bcrypt.hash()` toma dos parámetros: el valor de la contraseña (`this.password`) y el número de rondas de sal (en este caso, **10**).
   - El número de rondas de sal determina la "fortaleza" de la encriptación: a más rondas, mayor seguridad, pero también mayor tiempo de procesamiento.

4. **`next();`**:
   - Finalmente, se llama a `next()` para continuar con el proceso de guardado del documento. Esto permite que el documento, ahora con la contraseña encriptada, se guarde en la base de datos.

### Resumen:
Este middleware asegura que **la contraseña se encripte antes de guardarse** en la base de datos, protegiéndola contra accesos no autorizados. Solo encripta la contraseña si ha sido modificada, lo que mejora el rendimiento.


> [!NOTE]
>funciona tanto al registrar un nuevo usuario como al actualizar la contraseña de un usuario existente.
>
> #### Explicacion:
>
>1. [(Registro de usuario (nuevo):]
> - Cuando un nuevo usuario se registra, se pasa su contraseña como texto claro al modelo de Mongoose.
> - El middleware detecta que la contraseña ha sido modificada (porque es la primera vez que se guarda).
> - La contraseña se encripta usando bcrypt antes de ser guardada en la base de datos.
>
> 2. [Actualización de la contraseña:]
> Si un usuario existente decide cambiar su contraseña, el middleware detecta que el campo password ha sido modificado (cuando se realiza la actualización).
> - La contraseña se encripta nuevamente utilizando bcrypt antes de guardarse en la base de datos.
>
> [En resumen:]
> El middleware asegura que siempre que el campo password se modifica, ya sea al registrar un usuario o actualizar la contraseña de un usuario existente, la contraseña se encripte correctamente antes de ser guardada. Esto garantiza que las contraseñas nunca se almacenen en texto claro, mejorando la seguridad.


