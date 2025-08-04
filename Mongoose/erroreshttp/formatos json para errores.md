## Formato JSON Recomendado para Errores

El formato JSON recomendado para las respuestas de error en una API es aquel que es **claro, consistente y fácil de procesar** tanto para los desarrolladores como para las máquinas. No existe un estándar oficial único, pero la mayoría de las API REST exitosas siguen un conjunto de principios comunes.

El objetivo es que un cliente pueda recibir un error y saber exactamente qué pasó sin necesidad de interpretar mensajes de texto complejos.

Aquí te muestro un formato de error **sólido y ampliamente adoptado**, seguido de ejemplos para diferentes tipos de errores.

### 1\. Formato General Recomendado

Un buen formato de error debe incluir al menos un mensaje descriptivo y un código de error que el cliente pueda usar para la lógica de su aplicación.

```json
{
  "status": "error",
  "code": 404,
  "message": "Recurso no encontrado",
  "details": "El ticket con el ID '6687a4c7f5c71a3e498c8c67' no existe en la base de datos."
}
```

**Explicación de los campos:**

  * **`status`**: Un indicador general del estado de la respuesta. Usar `"error"` es una práctica común.
  * **`code`**: El código de estado HTTP (`404`, `400`, `500`, etc.). Es redundante pero útil para una referencia rápida, ya que el código se envía también en las cabeceras de la respuesta.
  * **`message`**: Un mensaje de error general y legible por humanos, como "Recurso no encontrado" o "Solicitud incorrecta".
  * **`details`**: Este campo es opcional, pero muy valioso. Proporciona una descripción más específica y técnica del error. Ayuda a los desarrolladores a diagnosticar el problema rápidamente.

-----

### 2\. Ejemplos de Errores Comunes

#### Ejemplo 1: Recurso No Encontrado (404 Not Found)

Este es el error que discutimos anteriormente. El cliente solicita un recurso que no existe.

```json
// GET /api/tickets/6687a4c7f5c71a3e498c8c67

{
  "status": "error",
  "code": 404,
  "message": "Recurso no encontrado",
  "details": "El ticket con el ID '6687a4c7f5c71a3e498c8c67' no fue encontrado."
}
```

#### Ejemplo 2: Solicitud Incorrecta (400 Bad Request)

Se usa cuando la solicitud del cliente está mal formada, por ejemplo, falta un campo obligatorio en el cuerpo de la solicitud (body) o un valor no es válido.

```json
// POST /api/tickets
// Body: { "subject": "Ticket de prueba" } <- Falta el campo "description"

{
  "status": "error",
  "code": 400,
  "message": "Solicitud incorrecta",
  "details": "Los campos 'description' y 'createdBy' son obligatorios. Por favor, revísalos."
}
```

En casos donde hay múltiples errores de validación, puedes usar un arreglo en el campo `details` o un campo adicional `errors` para ser más explícito.

```json
{
  "status": "error",
  "code": 400,
  "message": "Errores de validación",
  "details": [
    {
      "field": "description",
      "message": "La descripción es obligatoria."
    },
    {
      "field": "subject",
      "message": "El asunto debe tener al menos 5 caracteres."
    }
  ]
}
```

#### Ejemplo 3: Error del Servidor (500 Internal Server Error)

Este se usa para errores inesperados en el servidor, como una falla de la base de datos o un error de código no capturado. **Es crucial no exponer información sensible** como rastros de la pila (`stack traces`) al cliente.

```json
// GET /api/tickets

{
  "status": "error",
  "code": 500,
  "message": "Error interno del servidor",
  "details": "Ha ocurrido un error inesperado. Por favor, inténtelo de nuevo más tarde."
}
```

En un entorno de desarrollo, podrías incluir el rastreo de la pila para depuración, pero en producción, siempre debes usar un mensaje genérico.

-----

### Implementación en Express.js

Para implementar esto de forma consistente en tu API, puedes crear un **middleware de manejo de errores global** que capture los errores y les dé formato antes de enviarlos al cliente.

```javascript
// En tu archivo app.js, al final de todas tus rutas

// Middleware global de manejo de errores
app.use((err, req, res, next) => {
    // Si el error tiene un código de estado (como los de Mongoose de validación)
    const statusCode = err.statusCode || 500;
    const message = err.message || 'Error interno del servidor.';

    // No exponemos detalles de errores 500 en producción
    const details = process.env.NODE_ENV === 'production' ?
        'Ha ocurrido un error inesperado en el servidor.' :
        err.stack;

    // Puedes personalizar la respuesta según el tipo de error
    if (err.name === 'ValidationError') {
        const errors = Object.values(err.errors).map(el => el.message);
        return res.status(400).json({
            status: "error",
            code: 400,
            message: "Errores de validación",
            details: errors
        });
    }

    res.status(statusCode).json({
        status: "error",
        code: statusCode,
        message: message,
        details: details
    });
});
```

Este formato te proporciona una base sólida para crear respuestas de error que son tanto informativas para el desarrollador como seguras para el usuario final. Al ser consistente en todo tu API, reduces la complejidad en los clientes que la consumen.

[Volver al Indice](/home/villegas/git/dev-learnings/)


