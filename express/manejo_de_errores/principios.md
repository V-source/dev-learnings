# Manejo de errores

El **manejo de errores** (“Error Handling”) se refiere a cómo Express captura y procesa errores que ocurren tanto de forma **síncrona** como **asíncrona**. Express incluye un manejador de errores por defecto, de modo que no necesitas escribir el tuyo propio para comenzar.

## Capturar errores

Es importante asegurarse de que Express capture todos los errores que ocurren mientras se ejecutan los manejadores de rutas y middleware.

Los errores que ocurren en código **síncrono** dentro de manejadores de rutas o middleware no requieren trabajo adicional. Si el código síncrono lanza un error, Express lo capturará y lo procesará. Por ejemplo:

```js
app.get('/', (req, res) => {
  throw new Error('BROKEN') // Express lo atrapará por sí solo.
})
```

Para errores devueltos por funciones **asíncronas** invocadas desde manejadores de rutas o middleware, debes pasarlos a la función `next()`, donde Express los capturará y procesará. Por ejemplo:

```js
app.get('/', (req, res, next) => {
  fs.readFile('/file-does-not-exist', (err, data) => {
    if (err) {
      next(err) // Pasar errores a Express.
    } else {
      res.send(data)
    }
  })
})
```

A partir de **Express 5**, los manejadores de rutas y middleware que devuelvan una **Promise** llamarán automáticamente a `next(value)` cuando la promesa sea rechazada o lance un error. Por ejemplo:

```js
app.get('/user/:id', async (req, res, next) => {
  const user = await getUserById(req.params.id)
  res.send(user)
})
```

Si `getUserById` lanza un error o rechaza, `next` será llamado con el error lanzado o con el valor rechazado. Si no se proporciona un valor rechazado, `next` será llamado con un objeto `Error` por defecto proporcionado por el enrutador de Express.

Si pasas **algo** a la función `next()` (excepto la cadena `'route'`), Express considera que la solicitud actual está en estado de error y omitirá cualquier enrutamiento o middleware restante que no maneje errores.

Si el callback en una secuencia no proporciona datos, sino únicamente errores, puedes simplificar el código de esta forma:

```js
app.get('/', [
  function (req, res, next) {
    fs.writeFile('/inaccessible-path', 'data', next)
  },
  function (req, res) {
    res.send('OK')
  }
])
```

En el ejemplo anterior, `next` se proporciona como callback para `fs.writeFile`, que se llama con o sin errores. Si no hay error, se ejecuta el segundo manejador; de lo contrario, Express captura y procesa el error.

Debes capturar errores que ocurran en código asíncrono invocado desde manejadores o middleware y pasarlos a Express para su procesamiento. Por ejemplo:

```js
app.get('/', (req, res, next) => {
  setTimeout(() => {
    try {
      throw new Error('BROKEN')
    } catch (err) {
      next(err)
    }
  }, 100)
})
```

El ejemplo anterior usa un bloque `try…catch` para capturar errores en el código asíncrono y pasarlos a Express. Si se omite el `try…catch`, Express no capturará el error, porque no forma parte del código síncrono del manejador.

Usa promesas para evitar la sobrecarga del `try…catch` o cuando empleas funciones que devuelven promesas. Por ejemplo:

```js
app.get('/', (req, res, next) => {
  Promise.resolve().then(() => {
    throw new Error('BROKEN')
  }).catch(next) // Los errores serán pasados a Express.
})
```

Como las promesas capturan automáticamente tanto errores síncronos como promesas rechazadas, puedes simplemente usar `next` como manejador final `catch` y Express capturará los errores, porque el manejador `catch` recibe el error como primer argumento.

También podrías usar una cadena de manejadores para confiar en el manejo de errores síncronos, reduciendo el código asíncrono a algo más trivial. Por ejemplo:

```js
app.get('/', [
  function (req, res, next) {
    fs.readFile('/maybe-valid-file', 'utf-8', (err, data) => {
      res.locals.data = data
      next(err)
    })
  },
  function (req, res) {
    res.locals.data = res.locals.data.split(',')[1]
    res.send(res.locals.data)
  }
])
```

El ejemplo de arriba tiene un par de declaraciones triviales del resultado de `readFile`. Si `readFile` genera un error, lo pasará a Express; de lo contrario, vuelves rápidamente al ámbito de manejo de errores síncrono en el siguiente manejador de la cadena. Luego, el ejemplo procesa los datos. Si esto falla, el manejador de errores síncronos lo capturará. Si hubieras hecho este procesamiento dentro del callback de `readFile`, la aplicación podría finalizar y los manejadores de errores de Express no se ejecutarían.

Sea cual sea el método que uses, si quieres que los manejadores de errores de Express se llamen y que la aplicación sobreviva, debes asegurarte de que Express reciba el error.

## El manejador de errores por defecto

Express cuenta con un manejador de errores incorporado que se encarga de cualquier error que pueda encontrarse en la aplicación. Este middleware de manejo de errores por defecto se añade al final del conjunto de funciones middleware.

Si pasas un error a `next()` y no lo manejas en un manejador de errores personalizado, será procesado por el manejador de errores incorporado; el error se escribirá al cliente con el *stack trace*. El *stack trace* **no** se incluye cuando la aplicación se ejecuta en ambiente de producción.

Define la variable de entorno `NODE_ENV` como `production` para ejecutar la aplicación en modo producción.

Cuando se escribe un error, la siguiente información se agrega a la respuesta:

* `res.statusCode` se define a partir de `err.status` (o `err.statusCode`). Si este valor está fuera del rango 4xx o 5xx, se establecerá en 500.
* `res.statusMessage` se ajusta de acuerdo al código de estado.
* El cuerpo será el HTML del mensaje del código de estado cuando esté en entorno de producción; de lo contrario será `err.stack`.
* Cualquier encabezado especificado en un objeto `err.headers`.

Si llamas a `next()` con un error después de que hayas comenzado a escribir la respuesta (por ejemplo, si encuentras un error mientras haces *streaming* de la respuesta al cliente), el manejador de errores por defecto de Express cerrará la conexión y fallará la solicitud.

Por tanto, cuando agregues un manejador de errores personalizado, debes delegar al manejador de errores por defecto si los encabezados ya han sido enviados al cliente:

```js
function errorHandler (err, req, res, next) {
  if (res.headersSent) {
    return next(err)
  }
  res.status(500)
  res.render('error', { error: err })
}
```

Ten en cuenta que el manejador de errores por defecto puede activarse si llamas a `next()` con un error más de una vez, incluso si hay middleware de manejo de errores personalizado en su lugar.

Otros middleware de manejo de errores pueden definirse como cualquier otro middleware de Express, excepto que **tienen cuatro argumentos** en lugar de tres: `(err, req, res, next)`. Por ejemplo:

```js
app.use((err, req, res, next) => {
  console.error(err.stack)
  res.status(500).send('Something broke!')
})
```

Los manejadores de errores deben definirse al final, después de otras llamadas `app.use()` y rutas. Por ejemplo:

```js
const bodyParser = require('body-parser')
const methodOverride = require('method-override')

app.use(bodyParser.urlencoded({
  extended: true
}))
app.use(bodyParser.json())
app.use(methodOverride())
app.use((err, req, res, next) => {
  // lógica
})
```

Las respuestas desde dentro de una función middleware pueden tener cualquier formato, como una página de error en HTML, un mensaje simple o una cadena JSON.

Para fines organizativos (y en frameworks de nivel superior), puedes definir varios middleware de manejo de errores, de forma similar al uso de middleware normales. Por ejemplo, para definir un manejador de errores para solicitudes hechas con `XHR` y otras que no:

```js
const bodyParser = require('body-parser')
const methodOverride = require('method-override')
app.use(bodyParser.urlencoded({
  extended: true
}))
app.use(bodyParser.json())
app.use(methodOverride())
app.use(logErrors)
app.use(clientErrorHandler)
app.use(errorHandler)
```

En este ejemplo, el manejador genérico `logErrors` podría escribir la información de la solicitud y del error en `stderr`, por ejemplo:

```js
function logErrors (err, req, res, next) {
  console.error(err.stack)
  next(err)
}
```

También en este ejemplo, `clientErrorHandler` se define de la siguiente manera; en este caso, el error se pasa explícitamente al siguiente manejador:

```js
function clientErrorHandler (err, req, res, next) {
  if (req.xhr) {
    res.status(500).send({ error: 'Something failed!' })
  } else {
    next(err)
  }
}
```

Implementa la función “catch-all” `errorHandler` así (por ejemplo):

```js
function errorHandler (err, req, res, next) {
  res.status(500)
  res.render('error', { error: err })
}
```

Si tienes un manejador de rutas con múltiples funciones callback, puedes usar el parámetro `route` para saltar al siguiente manejador de ruta. Por ejemplo:

```js
app.get('/a_route_behind_paywall',
  (req, res, next) => {
    if (!req.user.hasPaid) {
      // continuar manejando esta solicitud
      next('route')
    } else {
      next()
    }
  }, (req, res, next) => {
    PaidContent.find((err, doc) => {
      if (err) return next(err)
      res.json(doc)
    })
  })
```

En este ejemplo, el manejador `getPaidContent` será omitido, pero cualquier manejador restante en `app` para `/a_route_behind_paywall` continuará ejecutándose.

Las llamadas a `next()` y `next(err)` indican que el manejador actual ha terminado y en qué estado. `next(err)` omitirá todos los manejadores restantes en la cadena, excepto aquellos que estén configurados para manejar errores, como se ha descrito arriba.

---

Si quieres, puedo también darte una versión aún más pulida o adaptada, o explicarte con ejemplos en español. ¿Te lo preparo?
