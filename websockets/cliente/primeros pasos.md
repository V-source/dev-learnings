# instalacion del cliente


## Standalone BUILD


Por defecto, el servidor de Socket.IO expone un cliente construido en la ruta `/socket.io/socket.io.js` y será registrado como una variable global en nuestro `javascript`

```javascript

<script src="/socket.io/socket.io.js"></script>
<script>
  const socket = io();
</script>

```
En casos donde no lo necesites en el cliente puedes deshabilitarlo desde el servidro de esta manera:

```javascript

const { Server } = require("socket.io");

const io = new Server({
  serveClient: false // deshabilita el cliente
});

```

## desde el CDN

```javascript
<script src="https://cdn.socket.io/4.8.1/socket.io.min.js" integrity="sha384-mkQ3/7FUtcGyoppY6bz/PORYoGqOl7/aSUMn2ymDOJcapfS6PHqxhRTMh1RR0Q6+" crossorigin="anonymous"></script>

```
**También hay otros CDN dsiponibles**
- cdnjs: [https://cdnjs.cloudflare.com/ajax/libs/socket.io/4.8.1/socket.io.min.js](https://cdnjs.cloudflare.com/ajax/libs/socket.io/4.8.1/socket.io.min.js)
- jsDelivr: [https://cdn.jsdelivr.net/npm/socket.io-client@4.8.1/dist/socket.io.min.js](https://cdn.jsdelivr.net/npm/socket.io-client@4.8.1/dist/socket.io.min.js)
- unpkg: [https://unpkg.com/socket.io-client@4.8.1/dist/socket.io.min.js](https://unpkg.com/socket.io-client@4.8.1/dist/socket.io.min.js)

Existen varias construcciones disponibles:

| name | bundle size | decription |
|------|-------------|------------|
| - socket.io.js  |  34.7 kB gzip  |  Unminified version, with debug  |
| - socket.io.min.js | 14.7 kB min+gzip | Production version, without debug |
| - socket.io.msgpack.min.js | 15.3 kB min+gzip | Production version, without debug and with the msgpack parser |


El paquete de [depuración](https://www.npmjs.com/package/debug) permite imprimir información de depuración en la consola. Puedes encontrar más información [aquí](https://socket.io/docs/v4/logging-and-debugging/).

Durante el desarrollo, recomendamos usar el paquete socket.io.js.
Al establecer localStorage.debug = 'socket.io-client:socket', cualquier evento recibido por el cliente se imprimirá en la consola.

Para producción, usa el paquete socket.io.min.js, que es una versión optimizada que excluye el paquete de depuración.
		
**en el contexto de vite:**

- Usar vite-plugin-remove-debug
```javascript
import { defineConfig } from 'vite'
import removeDebug from 'vite-plugin-remove-debug'

export default defineConfig({
  plugins: [removeDebug()]
})
```

- Solución específica para Vite
- Fácil de implementar
- Mantiene la funcionalidad de socket.io
- Requiere instalar un plugin adicional

Este plugin está diseñado específicamente para Vite y elimina el paquete de depuración de manera eficiente. Es la solución más directa y recomendada para tu caso.

## paquete NPM.



```bash

npm install socket.io-client

```
>[!NOTE] Para los usuarios de typescript. 
>La instalación de `@types/socket.io-client` ya no es necesaria porque ya están incluidos en el paquete.
>
>> La instalcación causaria errores como este:
>> _`Object literal may only specify known properties, and 'extraHeaders' does not exist in type 'ConnectOpts'`_




