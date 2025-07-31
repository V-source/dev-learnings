## Referencias en Mongoose: Usuario y Ticket

En Mongoose, las referencias te permiten establecer relaciones entre diferentes modelos (colecciones) en tu base de datos MongoDB. En este ejemplo, te mostraré cómo crear una relación donde un **Ticket** pertenece a un **Usuario**. Esto significa que cada ticket "sabe" qué usuario lo creó o a qué usuario está asociado.

### 1. Definición de los Modelos

Primero, definamos nuestros dos modelos: `User` (Usuario) y `Ticket`.

```javascript
const mongoose = require("mongoose");
const Schema = mongoose.Schema;

// --- Esquema de Usuario ---
const userSchema = new Schema({
  name: {
    type: String,
    required: true,
  },
  email: {
    type: String,
    required: true,
    unique: true,
  },
  // Puedes añadir más campos aquí, como 'password', 'createdAt', etc.
});

const User = mongoose.model("User", userSchema);

// --- Esquema de Ticket ---
const ticketSchema = new Schema({
  title: {
    type: String,
    required: true,
  },
  description: {
    type: String,
    required: true,
  },
  status: {
    type: String,
    enum: ["open", "closed", "pending"], // Opciones válidas para el estado
    default: "open",
  },
  // La clave aquí es la referencia al usuario
  // 'type: Schema.Types.ObjectId' indica que almacenaremos un ID de MongoDB
  // 'ref: 'User'' indica a qué modelo se refiere este ID
  user: {
    type: Schema.Types.ObjectId,
    ref: "User", // Importante: debe coincidir con el nombre del modelo exportado para User
    required: true,
  },
  createdAt: {
    type: Date,
    default: Date.now,
  },
});

const Ticket = mongoose.model("Ticket", ticketSchema);

module.exports = { User, Ticket };
```

**Explicación de la referencia:**

En el `ticketSchema`, la clave crucial es el campo `user`:

```javascript
user: {
    type: Schema.Types.ObjectId,
    ref: 'User',
    required: true
}
```

- `type: Schema.Types.ObjectId`: Le dice a Mongoose que este campo almacenará un `ObjectId` de MongoDB. Este `ObjectId` será el identificador único del documento `User` al que hace referencia.
- `ref: 'User'`: Esta es la parte más importante para las referencias. Le indica a Mongoose que el `ObjectId` almacenado en este campo se refiere a un documento en la colección asociada con el modelo `'User'`. Es fundamental que el valor de `ref` (`'User'`) coincida exactamente con el nombre que usaste al definir el modelo `User` (`mongoose.model('User', userSchema);`).

### 2. Creación y Asociación de Datos

Ahora, veamos cómo crear un usuario y luego crear tickets asociados a ese usuario.

```javascript
const mongoose = require("mongoose");
const { User, Ticket } = require("./your_models_file"); // Asegúrate de que la ruta sea correcta

mongoose
  .connect("mongodb://localhost:27017/your_database_name", {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  })
  .then(() => console.log("Conectado a MongoDB"))
  .catch((err) => console.error("Error de conexión a MongoDB:", err));

async function createData() {
  try {
    // 1. Crear un nuevo usuario
    const newUser = new User({
      name: "Juan Pérez",
      email: "juan.perez@example.com",
    });
    const savedUser = await newUser.save();
    console.log("Usuario creado:", savedUser);

    // 2. Crear tickets y asociarlos al usuario creado
    const ticket1 = new Ticket({
      title: "Problema con la conexión a internet",
      description: "No puedo acceder a ninguna página web.",
      user: savedUser._id, // Asignamos el ID del usuario al campo 'user'
    });
    await ticket1.save();
    console.log("Ticket 1 creado:", ticket1);

    const ticket2 = new Ticket({
      title: "Error en la aplicación móvil",
      description: "La aplicación se cierra inesperadamente.",
      user: savedUser._id, // Asignamos el mismo ID de usuario
    });
    await ticket2.save();
    console.log("Ticket 2 creado:", ticket2);
  } catch (error) {
    console.error("Error al crear datos:", error);
  } finally {
    mongoose.disconnect(); // Desconectar al finalizar
  }
}

createData();
```

Cuando guardas los tickets, Mongoose almacenará el `_id` del `savedUser` en el campo `user` de cada documento `Ticket`.

### 3. Recuperación de Datos con `populate`

El verdadero poder de las referencias se ve cuando necesitas obtener los datos del documento referenciado. Para esto, Mongoose usa el método `populate()`.

```javascript
const mongoose = require("mongoose");
const { User, Ticket } = require("./your_models_file"); // Asegúrate de que la ruta sea correcta

mongoose
  .connect("mongodb://localhost:27017/your_database_name", {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  })
  .then(() => console.log("Conectado a MongoDB"))
  .catch((err) => console.error("Error de conexión a MongoDB:", err));

async function findTicketsWithUser() {
  try {
    // Encontrar todos los tickets y "popular" el campo 'user'
    // Esto reemplazará el ObjectId del usuario con el documento completo del usuario
    const tickets = await Ticket.find().populate("user");
    // También puedes especificar qué campos del usuario quieres
    // const tickets = await Ticket.find().populate('user', 'name email');

    console.log("\n--- Tickets con información de usuario ---");
    tickets.forEach((ticket) => {
      console.log(`\nTicket ID: ${ticket._id}`);
      console.log(`Título: ${ticket.title}`);
      console.log(`Descripción: ${ticket.description}`);
      console.log(`Estado: ${ticket.status}`);
      console.log(`Creado por: ${ticket.user.name} (${ticket.user.email})`);
      console.log(`ID de Usuario: ${ticket.user._id}`);
    });

    // Encontrar un usuario y "popular" sus tickets (si hubiéramos definido la referencia inversa)
    // Esto requeriría añadir un campo de referencia inversa en el modelo User o usar virtuals.
    // Por ejemplo, en UserSchema:
    // userSchema.virtual('tickets', {
    //     ref: 'Ticket',
    //     localField: '_id',
    //     foreignField: 'user'
    // });
    // Y luego:
    // const userWithTickets = await User.findOne({ email: 'juan.perez@example.com' }).populate('tickets');
    // console.log('\nUsuario con sus tickets:', userWithTickets);
  } catch (error) {
    console.error("Error al buscar datos:", error);
  } finally {
    mongoose.disconnect();
  }
}

findTicketsWithUser();
```

Cuando llamas a `.populate('user')` en la consulta `Ticket.find()`, Mongoose realiza una consulta adicional en la colección `users` usando el `ObjectId` almacenado en el campo `user` de cada ticket. Luego, reemplaza ese `ObjectId` con el documento completo del usuario (o los campos que hayas especificado).

### Resumen

Las referencias en Mongoose son una forma eficiente de modelar relaciones "uno a muchos" o "uno a uno" entre tus colecciones. Usas `Schema.Types.ObjectId` para el tipo de campo y `ref` para indicar a qué modelo apunta. Luego, el método `populate()` te permite cargar los documentos referenciados cuando sea necesario, lo que es muy útil para obtener datos relacionados en una sola consulta.

¿Te gustaría ver un ejemplo de cómo manejar una relación inversa, como que un usuario tenga una lista de sus tickets?
