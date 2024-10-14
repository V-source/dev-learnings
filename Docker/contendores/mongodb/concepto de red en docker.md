# Concepto de red en Docker


Docker permite que varios contenedores se comuniquen entre sí a través de una red compartida. Cuando contenedores están en la misma red, pueden accederse mutuamente usando el nombre del contenedor como si fuera el hostname o dirección. Esto es útil para aplicaciones que tienen varios servicios, como una API, una base de datos, etc.

## Explicación del comando para 


```bash
docker run --name mongodb -d --network mongodb mongodb/mongodb-community-server:$MONGODB_VERSION
```
Este comando crea y ejecuta un contenedor de MongoDB en segundo plano (`-d`), con las siguientes opciones:

1. **`--name mongodb`**: Le asigna el nombre `mongodb` al contenedor, facilitando su identificación. Este nombre se convierte en el hostname que otros contenedores en la misma red pueden usar para acceder a este contenedor.

2. **`--network mongodb`**: Le indica a Docker que conecte el contenedor a una red específica llamada `mongodb`. Si la red no existe, deberías crearla primero con `docker network create mongodb`. Esta opción permite que otros contenedores que estén en la misma red accedan a MongoDB usando `mongodb` como nombre de host.

3. **`mongodb/mongodb-community-server:$MONGODB_VERSION`**: Especifica la imagen de MongoDB que se usará para crear el contenedor, con `$MONGODB_VERSION` representando la versión específica que quieras (por ejemplo, `6.0`).


## Uso dentro de la red compartida


Si tienes otros contenedores, como por ejemplo una aplicación Node.js que se ejecuta en otro contenedor, puedes conectarla a la misma red `mongodb` usando el parámetro `--network mongodb`. Desde dentro de este contenedor, podrías conectarte a MongoDB mediante la URI:
```bash
mongodb://mongodb:27017
```
En este caso:
- `mongodb` se refiere al nombre del contenedor que ejecuta MongoDB. Docker sabe cómo resolver el nombre a la dirección IP del contenedor dentro de la red `mongodb`.
- `27017` es el puerto por defecto de MongoDB.


## Ejemplo de uso en otro contenedor


Supongamos que tienes una aplicación que necesita conectarse a MongoDB. Podrías ejecutarla de esta manera:
```bash
docker run --name mi-app --network mongodb mi-imagen-de-app
```
Dentro de `mi-imagen-de-app`, podrías conectar tu aplicación a la URI `mongodb://mongodb:27017` para acceder a MongoDB en el contenedor llamado `mongodb`.

Esto permite que ambos contenedores interactúen como si estuvieran en la misma red local, aislados del resto de la red de tu máquina y accesibles entre sí únicamente a través de Docker.


---

esto es util para no tener que instalar todo un entorno de desarrollo en el sistema.
