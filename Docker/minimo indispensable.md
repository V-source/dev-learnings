Aquí tienes una guía rápida para comenzar a usar Docker con lo mínimo indispensable:

### 1. Conceptos Básicos
- **Contenedores**: Son unidades ejecutables de software que empaquetan el código y todas sus dependencias.
- **Imágenes**: Son plantillas a partir de las cuales se crean los contenedores. 
- **Docker Hub**: Es un repositorio donde puedes encontrar imágenes oficiales y de la comunidad.

### 2. Comandos Esenciales

#### Verificar que Docker esté funcionando
```bash
docker --version
docker info
```

#### Buscar Imágenes
```bash
docker search <nombre_imagen>
```
Por ejemplo:
```bash
docker search nginx
```

#### Descargar una Imagen
```bash
docker pull <nombre_imagen>
```
Por ejemplo:
```bash
docker pull nginx
```

#### Ejecutar un Contenedor
```bash
docker run <nombre_imagen>
```
Ejemplo básico:
```bash
docker run nginx
```

Para ejecutar un contenedor en segundo plano (modo *detached*) y exponer un puerto específico, usa:
```bash
docker run -d -p <puerto_local>:<puerto_contenedor> <nombre_imagen>
```
Ejemplo:
```bash
docker run -d -p 8080:80 nginx
```
Esto expondrá el puerto 80 del contenedor en el puerto 8080 de tu máquina local.

#### Ver Contenedores Activos
```bash
docker ps
```

Para ver todos los contenedores (incluso los que están detenidos):
```bash
docker ps -a
```

#### Detener un Contenedor
```bash
docker stop <id_contenedor>
```

#### Eliminar un Contenedor
```bash
docker rm <id_contenedor>
```

#### Eliminar una Imagen
```bash
docker rmi <id_imagen>
```

### 3. Dockerfile (opcional pero útil)
Un **Dockerfile** es un archivo de configuración para crear imágenes personalizadas. Su estructura básica es:
```dockerfile
# Usa una imagen base
FROM nginx

# Copia archivos
COPY . /usr/share/nginx/html

# Define el puerto
EXPOSE 80

# Comando de inicio
CMD ["nginx", "-g", "daemon off;"]
```
Luego, construye la imagen con:
```bash
docker build -t mi_imagen .
```

### 4. Comandos Útiles de Docker Compose (opcional)
Si manejas varios contenedores, puedes usar **Docker Compose** con un archivo `docker-compose.yml`:
```yaml
version: '3'
services:
  web:
    image: nginx
    ports:
      - "8080:80"
```
Inicia todos los servicios con:
```bash
docker-compose up
```
Detén todos los servicios con:
```bash
docker-compose down
```

Con esta guía ya puedes empezar a trabajar con Docker y ejecutar contenedores básicos. ¡Explora las imágenes en Docker Hub y experimenta!
