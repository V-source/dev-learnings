# EJECUTAR MONGODB COMO CONTENEDOR.

```bash
export MONGODB_VERSION=6.0-ubi8
docker run --name mongodb -d mongodb/mongodb-community-server:$MONGODB_VERSION
```

Este comando iniciará un servidor MongoDB que ejecute la versión 6.0 basada en Red Hat UBI en modo independiente (como un proceso en segundo plano). Como práctica recomendada , se recomienda utilizar una etiqueta para especificar la versión de MongoDB para garantizar la coherencia.

**`export MONGODB_VERSION=6.0-ubi8`:** establece la version que se quiere utilizar de mongo

**`docker run --name mongodb -d mongodb/mongodb-community-server:$MONGODB_VERSION`:** ejecuta el contendor de mongo con la version predefinida 

# ACCEDER A MONGODB DESDE OTRA APLICACION LOCAL.


```bash
docker run --name mongodb -d -p 27017:27017 mongodb/mongodb-community-server:$MONGODB_VERSION
```

Con este método, podrá conectarse a su instancia de MongoDB en mongodb://localhost:27017. Puede probarlo con Compass , la interfaz gráfica de usuario de MongoDB para visualizar y analizar sus datos.

> [!NOTE]
> Cualquier dato creado como parte del ciclo de vida de ese contenedor se destruirá una vez que se elimine el contenedor. Puede hacerlo con los comandos Docker stop y rm. Sin formato

# DETENER EL CONTENEDOR DE MONGODB

```bash
docker stop mongodb && docker rm mongodb
```

# persistencia de datos.




# EJECUTA MONGODB
docker run --name mongodb -p 27017:27017 -v $(pwd)/data:/data/db mongodb/mongodb-community-server:$MONGODB_VERSION
