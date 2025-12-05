# PREGUNTA INICIAL.

Perfecto. Ahora vamos a cambiar de especialidad. Ya no estamos en la sala de emergencias, sino en el consultorio de autenticación.

La anatomía de un middleware de autenticación es fascinante. Es un ganglio nervioso que no solo verifica una señal, sino que también produce una nueva: el JWT (JSON Web Token). Imagina que el sistema de tu aplicación es un cuerpo humano. El intento de login es como la presentación de una credencial de identidad (nombre de usuario y contraseña). El middleware es el biomarcador que, si la credencial es válida, genera una nueva "célula" de identidad digital, el JWT. Esta célula tiene una vida limitada y se usará para todas las interacciones futuras.

La Anatomía del Middleware de Autenticación y JWT
Este middleware es un "ganglio" de tres órganos, como un middleware normal. A diferencia del de errores, no tiene el órgano err porque su objetivo no es manejar patologías, sino verificar y producir.

JavaScript

app.use(async (req, res, next) => {
  // Aquí ocurre la verificación y la producción del token
});
Veamos cada órgano y su función específica en este contexto.

1. req (El Paciente)
El objeto de la solicitud (request). Este es el "paciente" que presenta sus credenciales. El middleware examina su cuerpo (req.body) para encontrar el nombre de usuario y la contraseña. También puede buscar información en los encabezados (req.headers) si el usuario ya tiene un token y este middleware se usa para verificarlo.

2. res (La Prescripción)
El objeto de la respuesta (response). Este es el "mecanismo de prescripción". Si la validación es exitosa, el middleware usa res para enviar la "medicina" al cliente: el JWT. Este token se envía generalmente en un objeto JSON en el cuerpo de la respuesta, junto con un mensaje de éxito. Por ejemplo: res.status(200).json({ token: jwt, message: 'Login exitoso' }).

3. next (El Flujo Sanguíneo)
El "fluido" que permite que la señal continúe su camino. En un login, el next tiene dos comportamientos muy diferentes, dependiendo del resultado:

Si la validación es exitosa: El middleware no llama a next(). ¿Por qué? Porque su trabajo ha terminado. La credencial ha sido validada, el token ha sido generado y enviado. No hay necesidad de que la solicitud continúe a otra "ruta" o "ganglio" porque el proceso de login en sí mismo ha concluido. El middleware de login es el punto final de esa ruta específica.

Si la validación falla (credenciales incorrectas): El middleware usa el res para enviar una respuesta de error (ejemplo: res.status(401).json({ message: 'Credenciales inválidas' })). Al igual que en el caso de éxito, no llama a next(), ya que el flujo de la solicitud debe detenerse. Se ha encontrado una "patología" y se ha diagnosticado en el lugar.

Anatomía del JWT (La Célula de Identidad)
El JWT es una "célula" especial, una cadena de caracteres con una estructura muy específica. Se compone de tres partes, separadas por puntos (.), como una molécula con tres secciones.

header.payload.signature
Header (La Cabeza): Contiene metadatos, como el tipo de token (JWT) y el algoritmo de encriptación (por ejemplo, HS256). Es como la etiqueta de la célula.

Payload (El Cuerpo): Contiene la información real, o "data genética", que quieres almacenar. Por ejemplo, el id del usuario, su rol y la fecha de expiración del token. ¡Importante! El payload no está encriptado, solo codificado en Base64. Es visible para cualquiera que tenga el token, por lo que nunca debes almacenar información sensible como la contraseña.

Signature (La Firma): La parte más importante para la seguridad. Es un hash generado a partir del header, el payload y una clave secreta privada que solo tú conoces. La signature es como la huella dactilar de la célula. Si alguien intenta alterar el header o el payload, la firma no coincidirá, y el sistema sabrá que el token ha sido manipulado y es inválido.

El Proceso Completo (La Biosíntesis del Token)
Un usuario envía una solicitud POST a /login con su username y password en el cuerpo.

El middleware recibe la solicitud (req) y accede a los datos.

Valida las credenciales contra la base de datos.

Si son válidas, crea un objeto payload con la información que necesita.

Usa una biblioteca como jsonwebtoken para "firmar" el token, combinando el header, el payload y la clave secreta.

Envía el token en la respuesta (res).

A partir de ese momento, el usuario, con su "célula de identidad" (el JWT), puede enviarla en los encabezados de futuras solicitudes. Otros middleware de tu aplicación la recibirán, la "examinarán" para verificar su validez y permitirán el acceso a recursos protegidos.

¿Qué parte de este proceso te gustaría diseccionar con más detalle?

---
---
## PROFUNDIZAR.

Vamos a abrir el quirófano y a disecar el proceso de generación del token en sí mismo. Ya hemos visto los órganos del middleware (req, res, next). Ahora nos enfocaremos en la "biología molecular" de cómo se crea el JWT.

Para generar el token, utilizaremos la biblioteca jsonwebtoken, que es el bisturí estándar para esta "cirugía" en el ecosistema de Node.js.

Anatomía de la Generación del Token (La Biosíntesis)
El proceso de creación del token se puede dividir en tres pasos clave.

1. El Payload (El ADN)
El payload es el material genético del token. Es un objeto de JavaScript simple que contiene toda la información no sensible que quieres incrustar. Es crucial recordar que esta información no está encriptada, solo codificada, así que cualquier persona que intercepte el token podrá leerla.

Los campos más comunes en el payload son:

id: El identificador único del usuario.

role: El rol del usuario (administrador, editor, cliente, etc.).

exp: La fecha de expiración del token. Esto es vital para la seguridad.

```JavaScript

// Ejemplo de un payload de usuario
const payload = {
  id: user._id, // Asume que 'user' es el objeto de usuario de la base de datos
  role: user.role,
};
2. La Clave Secreta (La Huella Genética)
La clave secreta es la "huella genética" única de tu aplicación. Es una cadena de texto larga y compleja que se utiliza para "firmar" el token. Esta firma es lo que garantiza que el token no ha sido alterado. ¡Esta clave debe ser protegida como el oro! Nunca la subas a un repositorio público ni la compartas. Lo ideal es guardarla en una variable de entorno.

JavaScript

// La clave secreta para firmar el token.
// Debería estar en un archivo .env
const secretKey = process.env.JWT_SECRET;
```
3. La Firma (La "Síntesis" Final)
Aquí es donde entra en juego la biblioteca jsonwebtoken. La función `jwt.sign()` es el "enzima" que toma el payload y la secretKey y, en base a un algoritmo (HS256 por defecto), los combina para crear la firma final.

Estructura de la función `jwt.sign()`:

```JavaScript

jwt.sign(payload, secretKey, [options, callback]);
payload: El objeto con los datos del usuario.

secretKey: La clave secreta de tu aplicación.

options (opcional): Un objeto para configurar la firma, donde la opción más importante es expiresIn para definir la vida del token.

JavaScript

const jwt = require('jsonwebtoken');

// Datos que irán en el token
const payload = {
  id: '60c72b2f9b1d7d3d8c8b4567', // ID de ejemplo
  role: 'admin',
};

// La clave secreta de tu aplicación (debe ser muy segura)
const secretKey = 'miSuperSecretaClaveParaFirmarTokens';

// Opciones de configuración: el token expira en 1 hora
const options = {
  expiresIn: '1h',
};

// --- La Biogénesis del Token ---
```const token = jwt.sign(payload, secretKey, options);`

// Ahora 'token' es una cadena de texto con la forma 'header.payload.signature'
console.log(token);
```
Este token resultante es la "célula de identidad" que enviamos al cliente. Una vez que el cliente la recibe, la guarda y la adjunta en el header de cada solicitud subsiguiente a rutas protegidas.

El Flujo Completo en el Middleware de login
Combinando todo, el código completo de tu middleware de login se vería así:

```JavaScript

const jwt = require('jsonwebtoken');
const User = require('../models/User'); // Tu modelo de Mongoose
const secretKey = process.env.JWT_SECRET;

const login = async (req, res, next) => {
  try {
    const { username, password } = req.body;

    // 1. Diagnóstico de credenciales: encuentra al usuario en la base de datos
    const user = await User.findOne({ username });
    if (!user) {
      return res.status(401).json({ message: 'Credenciales inválidas' });
    }

    // 2. Verificación: compara la contraseña enviada con la guardada
    const isMatch = await user.comparePassword(password);
    if (!isMatch) {
      return res.status(401).json({ message: 'Credenciales inválidas' });
    }

    // 3. Biosíntesis: crea el payload y firma el token
    const payload = {
      id: user._id,
      role: user.role,
    };

    const token = jwt.sign(payload, secretKey, {
      expiresIn: '1h', // El token expira en 1 hora
    });

    // 4. Prescripción: envía el token al cliente
    res.status(200).json({
      message: 'Login exitoso',
      token: token,
    });
  } catch (error) {
    next(error); // Si hay un error inesperado, se lo pasa al middleware de errores
  }
};
```
Este es el proceso completo, desde que el req llega hasta que el res es enviado con el token. Es una secuencia lógica y vital en el sistema de autenticación de cualquier API.

¿Te queda clara la función de cada una de estas "moléculas" en la generación del token?
