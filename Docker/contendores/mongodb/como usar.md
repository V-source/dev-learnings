## Prerrequisitos

Para ejecutar las siguientes instrucciones, necesitarás instalar Docker Desktop. Puedes encontrar las instrucciones de instalación para tu sistema operativo en su [sitio web](https://www.docker.com).

## Ejecutando MongoDB como un Contenedor Docker

Puedes iniciar un contenedor de MongoDB utilizando Docker con el siguiente comando. Comienza configurando la versión que quieres usar en una variable.

```bash
export MONGODB_VERSION=6.0-ubi8
docker run --name mongodb -d mongodb/mongodb-community-server:$MONGODB_VERSION
```

Este comando iniciará un servidor MongoDB ejecutando la versión 6.0 basada en **Red Hat UBI** en modo **detached** (como un proceso en segundo plano). Como **mejor práctica**, se recomienda usar una etiqueta (tag) para especificar la versión de MongoDB y asegurar consistencia.

## Acceder a mongodb desde otra aplicaión local.

Si necesitas acceder al servidor MongoDB desde otra aplicación que esté corriendo localmente, tendrás que exponer un puerto usando el argumento `-p`.

```bash
docker run --name mongodb -d -p 27017:27017 mongodb/mongodb-community-server:$MONGODB_VERSION
```

Usando este método, podrás conectarte a tu instancia de MongoDB en `mongodb://localhost:27017`. Puedes probarlo con **Compass**, la interfaz gráfica de MongoDB para visualizar y analizar tus datos.

Cualquier dato creado durante el ciclo de vida de ese contenedor será destruido una vez que se elimine el contenedor. Puedes detener y eliminar el contenedor con los siguientes comandos:

```bash
docker stop mongodb && docker rm mongodb
```

## Persitencia de datos.

Si deseas **persistir** los datos en tu máquina local, puedes montar un volumen usando el argumento `-v`.

```bash
docker run --name mongodb -d -p 27017:27017 -v $(pwd)/data:/data/db mongodb/mongodb-community-server:$MONGODB_VERSION
```

Si detienes y vuelves a iniciar el contenedor, todos los datos que ingresaste previamente seguirán estando allí.

Si tu aplicación se está ejecutando dentro de un contenedor, puedes ejecutar MongoDB como parte de la misma red Docker que tu aplicación usando `--network`. Con este método, te conectarás a MongoDB en `mongodb://mongodb:27017` desde las otras aplicaciones en contenedores dentro de la misma red.

```bash
docker run --name mongodb -d --network mongodb mongodb/mongodb-community-server:$MONGODB_VERSION
```

Puedes probar esto con un segundo contenedor que ejecute **mongosh** en la misma red.

```bash
docker run --name mongosh --network mongodb mongodb/mongodb-community-server:$MONGODB_VERSION mongosh mongodb://mongodb --eval "show dbs"
```

Para inicializar tu MongoDB con un usuario root, puedes usar las variables de entorno `MONGO_INITDB_ROOT_USERNAME` y `MONGO_INITDB_ROOT_PASSWORD`. Estas variables crearán un usuario con permisos de root con el nombre de usuario y contraseña especificados.

```bash
docker run --name mongodb -d -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=user -e MONGO_INITDB_ROOT_PASSWORD=pass mongodb/mongodb-community-server:$MONGODB_VERSION
```

Recuerda que puedes combinar estos argumentos según tus necesidades.

## Conectando a MongoDB desde otro Contenedor Docker

A menudo, tu aplicación estará ejecutándose dentro de un contenedor y necesitarás conectarte a una base de datos que esté corriendo fuera de ese contenedor. La mejor forma de hacerlo es usando variables de entorno. Al usar una variable de entorno, podrás establecer la cadena de conexión a un valor diferente dependiendo de dónde estés ejecutando ese contenedor.

Por ejemplo, podrías tener el mismo contenedor ejecutándose tanto en tu entorno de producción como en tus servidores de desarrollo. Tu aplicación de producción probablemente se conectará a **MongoDB Atlas** o a un servidor **MongoDB Enterprise** en contenedor, mientras que tu instancia de desarrollo se conectará a una instancia local de MongoDB. Una cadena de conexión que proporcione la dirección del servidor se pasará como una variable de entorno en el comando `docker run`.

```bash
docker run -d --name MYAPP -e MONGODB_CONNSTRING=mongodb+srv://username:password@clusterURL MYAPP:1.0
```

Usando variables de entorno, podrás conectarte a cualquier instancia de MongoDB, ya sea en la nube o localmente, desde tu aplicación que se esté ejecutando dentro de un contenedor. Puedes encontrar más información sobre cómo hacerlo siguiendo el tutorial completo en el **[MongoDB Developer Hub](https://www.mongodb.com/)**.

## Usando MongoDB con Docker Compose

Si tienes una aplicación y un contenedor MongoDB corriendo en la misma máquina, puedes usar **Docker Compose** para iniciarlos y detenerlos juntos. Docker Compose es más adecuado para entornos de desarrollo o prueba donde no necesitas todas las funcionalidades de MongoDB Enterprise o MongoDB Atlas.

En un archivo `docker-compose.yaml`, describe todos los contenedores que son parte de la aplicación. Uno de tus contenedores podría ser un servidor MongoDB. Como mejor práctica, deberías pasar tu cadena de conexión a tu aplicación como una variable de entorno, tal como se explicó en la sección anterior.

```yaml
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

Desde el directorio donde se encuentra el archivo, ejecuta el comando `docker-compose`.

```bash
docker-compose up
```

Este comando iniciará tanto tu aplicación como tu instancia local de MongoDB.

## Gestionando MongoDB desde un Contenedor

Para gestionar tu servidor MongoDB o acceder, importar y exportar tus datos, puedes usar un segundo contenedor MongoDB desde el cual ejecutar las herramientas CLI necesarias.

Para abrir una sesión de **Mongo Shell** a tu servidor **MongoDB Atlas**, usa **mongosh** y especifica la URL del clúster.

```bash
docker run -it mongodb/mongodb-community-server:$MONGODB_VERSION mongosh "mongodb://username:password@clusterURL/database"
```

Si quieres usar la herramienta **mongoexport** para exportar una colección existente a un archivo `.json`, puedes usar el comando desde un contenedor MongoDB separado. Necesitarás montar un volumen para poder acceder al archivo JSON resultante.

```bash
docker run -it -v $(pwd):/tmp mongodb/mongodb-community-server:$MONGODB_VERSION mongoexport --collection=COLLECTION --out=/tmp/COLLECTION.json "mongodb://username:password@clusterURL/database"
```

Si necesitas importar datos en una colección, usa la herramienta **mongoimport**, también disponible en la imagen `mongodb/mongodb-community-server:$MONGODB_VERSION`. Nuevamente, necesitarás montar un volumen para acceder a un archivo almacenado en tu máquina local desde dentro del contenedor.

```bash
docker run -it -v $(pwd):/tmp mongodb/mongodb-community-server:$MONGODB_VERSION mongoimport --drop --collection=COLLECTION "mongodb+srv://user:password@clusterURL/database" /tmp/COLLECTION.json
```

Cualquier otra herramienta que venga con una instalación de MongoDB puede ser accedida de la misma manera.

## ¿Cuáles son los beneficios de usar Docker?

Hay muchas ventajas de usar contenedores como parte de tu trabajo diario como desarrollador de software.

- **Consistencia**: Al usar tecnología de contenedores, puedes asegurarte de que todos en tu equipo usen exactamente los mismos entornos y configuraciones. También reduces significativamente la fricción en los despliegues, ya que el entorno de producción será consistente con tu entorno de desarrollo.
- **Ligero**: Los contenedores Docker son rápidos de iniciar y consumen pocos recursos en comparación con las máquinas virtuales.
- **Efímero**: Cualquier cambio en el sistema de archivos del contenedor será destruido al finalizar. Esta impermanencia asegura un entorno limpio en cada inicio.

---
