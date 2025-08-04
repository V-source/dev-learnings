Cuando la base de datos no tiene los datos solicitados, el código HTTP más apropiado para enviar en la respuesta es el **404 Not Found**.

-----

### Por qué el 404 es la mejor opción

El código **404 Not Found** significa que el servidor no pudo encontrar el recurso solicitado. En el contexto de una API, "recurso" se refiere a un ítem de datos específico, como un ticket con un ID determinado o un usuario con un nombre de usuario particular. Si intentas buscar un ticket con el ID `6687a4c7f5c71a3e498c8c67` y tu base de datos no contiene un documento con ese ID, la respuesta correcta es un 404.

  * **Claridad**: Le dice al cliente (navegador, aplicación móvil, etc.) de forma inequívoca que lo que buscaba no existe.
  * **Convención**: Es el estándar de facto para este tipo de escenarios en la web y en el desarrollo de APIs REST. El cliente sabe exactamente qué ha ocurrido y puede manejar la situación de forma adecuada.

### Ejemplo práctico en Express.js

```javascript
// Suponiendo que 'Ticket' es tu modelo de Mongoose
const Ticket = require('../models/tickets.model');

// Endpoint para obtener un ticket por su ID
app.get('/tickets/:id', async (req, res) => {
    try {
        const ticketId = req.params.id;
        const ticket = await Ticket.findById(ticketId);

        // Si la base de datos no encuentra el ticket, 'ticket' será null
        if (!ticket) {
            // En este caso, enviamos el código 404
            return res.status(404).json({
                message: "No se encontró el ticket con el ID proporcionado."
            });
        }

        // Si se encuentra el ticket, lo devolvemos con un 200 OK
        res.status(200).json(ticket);

    } catch (error) {
        // Manejamos posibles errores del servidor o un ID con formato inválido
        // Si el ID tiene un formato incorrecto (ej. '123' en vez de un ObjectId), Mongoose lanzará un error.
        if (error.name === 'CastError') {
            return res.status(400).json({
                message: "El formato del ID proporcionado es inválido."
            });
        }
        res.status(500).json({
            message: "Ha ocurrido un error en el servidor."
        });
    }
});
```

-----

### Otras consideraciones

  * **400 Bad Request**: Si la base de datos no tiene el dato porque la solicitud del cliente tiene un formato incorrecto (por ejemplo, el ID no es un `ObjectId` válido), el código **400 Bad Request** es una alternativa más precisa. Sin embargo, para un recurso que simplemente no existe, el **404** es la elección principal.
  * **204 No Content**: El código **204 No Content** se usa para respuestas exitosas que no tienen un cuerpo de respuesta. Esto podría usarse en una solicitud de eliminación (`DELETE`), donde el servidor confirma que el recurso ha sido eliminado correctamente pero no devuelve datos.

En resumen, utiliza **404 Not Found** cuando el recurso solicitado, aunque potencialmente válido en su forma, no se encuentra en tu colección de datos.


[Volver al Indice](/home/villegas/git/dev-learnings/)
