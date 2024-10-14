# Configuracion básica.


```yaml compose.yaml
version: '3'
services:
  myapplication:
    image: mongodb/mongodb-community-server:6.0-ubi8
    environment:
      - CONN_STR=mongodb://user:pass@mongodb
    command: '/bin/bash -c "sleep 5; mongosh $$CONN_STR --eval \"show dbs;\""'
    depends_on:
      - mongodb
  mongodb:
    image: mongodb/mongodb-community-server:6.0-ubi8
    environment:
      - MONGO_INITDB_ROOT_USERNAME=user
      - MONGO_INITDB_ROOT_PASSWORD=pass
    volumes:
      - type: bind
        source: ./data
        target: /data/db
```

Esta es una configuración de **Docker Compose** que define dos servicios: `myapplication` y `mongodb`. Vamos a desglosar cada línea y explicar su propósito.

### Estructura básica
```yaml
version: '3'
```
- **`version: '3'`**: Especifica la versión del archivo de configuración de Docker Compose. La versión 3 es comúnmente utilizada y es compatible con las características más recientes de Docker.

### Servicios
```yaml
services:
```
- **`services:`**: Esta clave indica que a continuación se definirán los servicios (contenedores) que se ejecutarán juntos.

### Servicio: myapplication
```yaml
  myapplication:
    image: mongodb/mongodb-community-server:6.0-ubi8
```
- **`myapplication:`**: Define un servicio llamado `myapplication`.
- **`image: mongodb/mongodb-community-server:6.0-ubi8`**: Especifica la imagen de Docker que se utilizará para este servicio. En este caso, parece que se está utilizando la imagen de MongoDB, aunque esto podría no ser correcto si realmente es una aplicación.

```yaml
    environment:
      - CONN_STR=mongodb://user:pass@mongodb
```
- **`environment:`**: Define variables de entorno que se pueden utilizar dentro del contenedor.
- **`- CONN_STR=mongodb://user:pass@mongodb`**: Establece la variable de entorno `CONN_STR` con la cadena de conexión a MongoDB. Esta cadena incluye el nombre de usuario (`user`), la contraseña (`pass`) y el hostname (`mongodb`), que es el nombre del servicio MongoDB definido más abajo. Esto permite a la aplicación conectarse a MongoDB.

```yaml
    command: '/bin/bash -c "sleep 5; mongosh $$CONN_STR --eval \"show dbs;\""'
```
- **`command:`**: Este comando se ejecuta cuando se inicia el contenedor. 
- **`/bin/bash -c "sleep 5; mongosh $$CONN_STR --eval \"show dbs;\""`**: 
  - **`sleep 5`**: Hace que el contenedor espere 5 segundos antes de ejecutar el siguiente comando. Esto es útil para asegurarse de que MongoDB esté completamente iniciado antes de que la aplicación intente conectarse.
  - **`mongosh $$CONN_STR --eval \"show dbs;\"`**: Usa `mongosh` para ejecutar un comando en MongoDB. 
    - `$$CONN_STR`: Expande la variable de entorno `CONN_STR` que definimos antes. 
    - `--eval "show dbs;"`: Indica que se ejecutará el comando `show dbs;` en MongoDB, que muestra las bases de datos disponibles.

```yaml
    depends_on:
      - mongodb
```
- **`depends_on:`**: Esta opción establece que el servicio `myapplication` depende de `mongodb`. Docker Compose asegura que el contenedor de `mongodb` se inicie antes de `myapplication`. Sin embargo, esto no garantiza que MongoDB esté completamente listo para aceptar conexiones, por eso se incluye el `sleep 5`.

### Servicio: mongodb
```yaml
  mongodb:
    image: mongodb/mongodb-community-server:6.0-ubi8
```
- **`mongodb:`**: Define un servicio llamado `mongodb`.
- **`image: mongodb/mongodb-community-server:6.0-ubi8`**: Especifica la imagen de Docker de MongoDB que se utilizará para este servicio.

```yaml
    environment:
      - MONGO_INITDB_ROOT_USERNAME=user
      - MONGO_INITDB_ROOT_PASSWORD=pass
```
- **`environment:`**: Define variables de entorno para el servicio de MongoDB.
- **`- MONGO_INITDB_ROOT_USERNAME=user`**: Establece el nombre de usuario raíz de MongoDB como `user`.
- **`- MONGO_INITDB_ROOT_PASSWORD=pass`**: Establece la contraseña del usuario raíz de MongoDB como `pass`. Estas variables se utilizan para crear un usuario administrador cuando se inicia MongoDB por primera vez.

```yaml
    volumes:
      - type: bind
        source: ./data
        target: /data/db
```
- **`volumes:`**: Esta opción define los volúmenes que se montarán en el contenedor, permitiendo la persistencia de datos.
- **`- type: bind`**: Indica que se usará un volumen de tipo "bind", que vincula una carpeta en el host a una carpeta en el contenedor.
- **`source: ./data`**: Especifica la ruta en el sistema host que se montará. En este caso, es una carpeta `data` en el directorio actual.
- **`target: /data/db`**: Especifica la ruta dentro del contenedor donde se montará el volumen. MongoDB almacena sus datos en `/data/db`, por lo que al montar esta carpeta, los datos se persistirán en la carpeta local `./data`.

### Resumen
- Este archivo de configuración define dos servicios: una aplicación que se conecta a MongoDB y el propio servicio de MongoDB.
- MongoDB está configurado para iniciar con un usuario y contraseña específicos, y los datos se almacenan en el sistema de archivos del host para asegurar la persistencia.
- La aplicación espera 5 segundos después de iniciar para asegurarse de que MongoDB esté listo antes de intentar conectarse y ejecutar un comando que muestra las bases de datos existentes.
