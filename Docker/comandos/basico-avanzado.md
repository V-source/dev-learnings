**guía completa y progresiva de comandos Docker**, pensada para que cualquier persona —desde cero— pueda entender y usar la herramienta paso a paso.

---

# 🐳 **Comandos Docker Esenciales para Todos los Niveles**

---

## 🚀 **1. Primeros Pasos: Iniciando con Docker**

Estos comandos te ayudan a entender qué es Docker, cómo obtener imágenes y lanzar tus primeros contenedores.

| Comando                | Descripción                                                           |                                     |
| ---------------------- | --------------------------------------------------------------------- | ----------------------------------- |
| `docker --version`     | Muestra la versión instalada de Docker.                               |                                     |
| `docker info`          | Muestra información del sistema Docker (imágenes, contenedores, etc). |                                     |
| `docker pull <imagen>` | Descarga una imagen desde Docker Hub. Ej: `docker pull nginx`         |                                     |
| `docker run <imagen>`  | Crea y ejecuta un contenedor desde una imagen.                        |                                     |
| `docker ps`            | Lista los contenedores que se están ejecutando.                       |                                     |
| `docker ps -a`         | Lista todos los contenedores (incluidos los detenidos).               |                                     |
| \`docker stop \<id     | nombre>\`                                                             | Detiene un contenedor en ejecución. |
| \`docker start \<id    | nombre>\`                                                             | Inicia un contenedor detenido.      |
| \`docker rm \<id       | nombre>\`                                                             | Elimina un contenedor.              |
| `docker rmi <imagen>`  | Elimina una imagen.                                                   |                                     |

📌 **Ejemplo básico:**

```bash
docker pull hello-world
docker run hello-world
```

---

## ⚙️ **2. Uso Diario: Trabajando con Contenedores**

Aquí empiezas a personalizar tus comandos y usar opciones más prácticas.

| Comando                               | Descripción                                             |                                                                |
| ------------------------------------- | ------------------------------------------------------- | -------------------------------------------------------------- |
| `docker run -it <imagen>`             | Ejecuta el contenedor en modo interactivo con terminal. |                                                                |
| `docker run -d <imagen>`              | Ejecuta el contenedor en segundo plano (modo demonio).  |                                                                |
| `docker run --name <nombre> <imagen>` | Asigna un nombre personalizado al contenedor.           |                                                                |
| \`docker logs \<nombre                | id>\`                                                   | Muestra los logs del contenedor.                               |
| \`docker exec -it \<nombre            | id> bash\`                                              | Abre una terminal dentro del contenedor (si tiene bash).       |
| \`docker top \<nombre                 | id>\`                                                   | Muestra los procesos que se están ejecutando en el contenedor. |
| \`docker inspect \<nombre             | id>\`                                                   | Muestra detalles técnicos en JSON del contenedor o imagen.     |

---

## 📁 **3. Volúmenes y Persistencia de Datos**

Para mantener datos incluso si el contenedor se borra.

| Comando                                                | Descripción                        |
| ------------------------------------------------------ | ---------------------------------- |
| `docker volume create <nombre>`                        | Crea un volumen persistente.       |
| `docker volume ls`                                     | Lista todos los volúmenes.         |
| `docker run -v <volumen>:/ruta/en/contenedor <imagen>` | Monta un volumen en un contenedor. |
| `docker volume rm <nombre>`                            | Elimina un volumen.                |
| `docker volume inspect <nombre>`                       | Muestra detalles del volumen.      |

📌 **Ejemplo práctico:**

```bash
docker volume create datos_app
docker run -v datos_app:/app/data ubuntu
```

---

## 🌐 **4. Redes en Docker**

Controlar cómo se comunican tus contenedores entre sí y con el exterior.

| Comando                                  | Descripción                                            |
| ---------------------------------------- | ------------------------------------------------------ |
| `docker network ls`                      | Lista todas las redes Docker.                          |
| `docker network create <nombre>`         | Crea una red personalizada.                            |
| `docker run --network <nombre> <imagen>` | Conecta un contenedor a una red específica.            |
| `docker network inspect <nombre>`        | Muestra detalles de una red.                           |
| `docker network rm <nombre>`             | Elimina una red (si no tiene contenedores conectados). |

---

## 🛠️ **5. Dockerfiles e Imágenes Personalizadas**

Aquí creas tus propias imágenes usando un archivo llamado `Dockerfile`.

| Comando                                             | Descripción                                                  |
| --------------------------------------------------- | ------------------------------------------------------------ |
| `docker build -t <nombre>:<tag> .`                  | Crea una imagen desde un Dockerfile en el directorio actual. |
| `docker images`                                     | Lista todas las imágenes descargadas o creadas.              |
| `docker tag <imagen> <usuario>/<repositorio>:<tag>` | Etiqueta una imagen para subirla a Docker Hub.               |
| `docker push <usuario>/<repositorio>:<tag>`         | Sube la imagen a Docker Hub.                                 |
| `docker history <imagen>`                           | Muestra el historial de capas de una imagen.                 |

📌 **Ejemplo básico de Dockerfile:**

```Dockerfile
FROM node:18
WORKDIR /app
COPY . .
RUN npm install
CMD ["npm", "start"]
```

---

## 🧼 **6. Limpieza y Mantenimiento**

Te ayuda a liberar espacio y mantener tu sistema Docker limpio.

| Comando                  | Descripción                            |
| ------------------------ | -------------------------------------- |
| `docker system prune`    | Elimina todo lo que no se esté usando. |
| `docker image prune`     | Elimina imágenes no usadas.            |
| `docker container prune` | Elimina contenedores detenidos.        |
| `docker volume prune`    | Elimina volúmenes no utilizados.       |

> ⚠️ Precaución: Estos comandos eliminan recursos y no hay forma de recuperarlos fácilmente.

---

## 🧪 **7. Avanzado: Docker Compose y Orquestación**

Permite ejecutar múltiples contenedores definidos en un solo archivo.

### 📄 `docker-compose.yml` ejemplo:

```yaml
version: "3"
services:
  web:
    image: nginx
    ports:
      - "8080:80"
  app:
    build: .
    volumes:
      - .:/app
    depends_on:
      - db
  db:
    image: postgres
    environment:
      POSTGRES_PASSWORD: ejemplo
```

| Comando                | Descripción                                                         |
| ---------------------- | ------------------------------------------------------------------- |
| `docker-compose up`    | Inicia todos los servicios.                                         |
| `docker-compose up -d` | Inicia en segundo plano.                                            |
| `docker-compose down`  | Detiene y elimina los contenedores, redes y volúmenes temporales.   |
| `docker-compose build` | Construye las imágenes desde Dockerfile definidos en los servicios. |
| `docker-compose logs`  | Muestra los logs de todos los servicios.                            |

---

## 🧠 **8. Diagnóstico y Debugging**

Para resolver errores y obtener información útil.

| Comando                          | Descripción                                               |
| -------------------------------- | --------------------------------------------------------- |
| `docker stats`                   | Muestra en tiempo real el uso de recursos por contenedor. |
| `docker diff <id>`               | Muestra cambios en el sistema de archivos del contenedor. |
| `docker events`                  | Muestra eventos del demonio Docker (útil para debugging). |
| `docker cp <cont>:<ruta> <host>` | Copia archivos desde un contenedor al host o viceversa.   |

---

## 📚 Recursos Sugeridos

* [https://docs.docker.com](https://docs.docker.com)
* [https://hub.docker.com](https://hub.docker.com)

---

¿Te gustaría que convierta esta guía en un PDF o en una mini cheat-sheet imprimible? También puedo darte ejemplos de proyectos completos o prácticas guiadas.
