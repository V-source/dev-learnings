
### 1. **Abrir `mongosh`**
   Asegúrate de que tienes instalado MongoDB y `mongosh`. Luego, abre una terminal y ejecuta:

   ```bash
   mongosh
   ```

### 2. **Seleccionar la base de datos `market`**
   Una vez en el shell de MongoDB, selecciona la base de datos `market`. Si no existe, se creará al asignarle un usuario:

   ```javascript
   use market
   ```

### 3. **Habilitar la autenticación (si no está activada)**

   La autenticación debe estar habilitada en tu servidor MongoDB para que los usuarios y contraseñas sean efectivos. Asegúrate de que `auth` está activado en el archivo de configuración de MongoDB (`mongod.conf`).

   Para habilitar la autenticación:
   - Edita el archivo `mongod.conf`.
   - Agrega o modifica la línea:

     ```yaml
     security:
       authorization: "enabled"
     ```

   - Reinicia MongoDB:

     ```bash
     sudo service mongod restart
     ```

### 4. **Crear el usuario con rol específico**

   Una vez habilitada la autenticación, crea un usuario con privilegios específicos para la base de datos `market`. Ejecuta el siguiente comando en `mongosh`:

   ```javascript
   db.createUser({
     user: "marketUser", // Cambia "marketUser" por el nombre de usuario que desees
     pwd: "securePassword123", // Cambia "securePassword123" por la contraseña deseada
     roles: [
       { role: "readWrite", db: "market" } // Otorga permisos de lectura y escritura
     ]
   });
   ```

### 5. **Verificar la creación del usuario**

   Para asegurarte de que el usuario fue creado correctamente, ejecuta:

   ```javascript
   db.getUsers()
   ```

   Esto mostrará todos los usuarios asociados a la base de datos `market`.

### 6. **Probar la conexión desde `mongosh`**

   Sal del shell y vuelve a conectarte con el nuevo usuario:

   ```bash
   mongosh "mongodb://marketUser:securePassword123@localhost:27017/market"
   ```

   Si la conexión es exitosa, significa que el usuario y la contraseña están funcionando correctamente.

### 7. **Configurar Mongoose en Express.js**

   En tu aplicación Express, usa la siguiente configuración para conectarte a la base de datos `market`:

   ```javascript
   const mongoose = require('mongoose');

   mongoose.connect('mongodb://marketUser:securePassword123@localhost:27017/market', {
     useNewUrlParser: true,
     useUnifiedTopology: true
   })
   .then(() => console.log('Conectado a MongoDB'))
   .catch(err => console.error('Error al conectar a MongoDB', err));
   ```

### Notas Adicionales:
- **Roles adicionales**: Si necesitas roles adicionales, como `dbAdmin`, puedes añadirlos al array `roles` en el paso 4.
- **Producción**: Usa una contraseña fuerte y considera almacenar las credenciales de manera segura, por ejemplo, en variables de entorno.

¿Te gustaría más detalles sobre alguno de estos pasos?



---
---


db.createUser({
  user: "villejsdev", // Cambia "marketUser" por el nombre de usuario que desees
  pwd: "villejsdev4248903254", // Cambia "securePassword123" por la contraseña deseada

  roles: [
    { role: "readWrite", db: "market" } // Otorga permisos de lectura y escritura
  ]
});
