Claro, aquí tienes los comandos más importantes para trabajar con imágenes en Docker, divididos por su función principal.

### Comandos para la gestión de imágenes locales

Estos comandos te ayudan a manejar las imágenes que ya tienes descargadas o construidas en tu máquina.

- `docker image ls` o `docker images`: Muestra una lista de todas las imágenes que tienes disponibles en tu sistema, con su ID, nombre, etiqueta, tamaño y fecha de creación. Es el comando más útil para saber qué tienes.
- `docker image inspect <nombre_imagen>`: Muestra información detallada sobre una imagen, como su configuración, capas, metadatos y etiquetas. Es muy útil para depurar y entender cómo está construida una imagen.
- `docker image pull <nombre_imagen>:<tag>`: Descarga una imagen desde un registro de Docker (como Docker Hub). Si omites el `:tag`, por defecto descargará la última versión (`latest`).
- `docker image rm <nombre_imagen>`: Elimina una o varias imágenes de tu máquina. No podrás eliminar una imagen si hay un contenedor que la está usando.
- `docker image prune`: Elimina todas las imágenes no utilizadas (`dangling images`). Esto es muy útil para liberar espacio en disco.

---

### Comandos para crear y subir imágenes

Estos comandos se centran en el proceso de construir tus propias imágenes personalizadas y compartirlas.

- `docker build -t <nombre_imagen>:<tag> .`: Este es el comando fundamental para crear una imagen a partir de un **Dockerfile**.
  - `-t`: Permite etiquetar la imagen con un nombre y una etiqueta para identificarla fácilmente.
  - `.`: El punto al final es crucial, ya que le dice a Docker que busque el `Dockerfile` en el directorio actual.
- `docker tag <nombre_imagen_actual>:<tag_actual> <nuevo_nombre_imagen>:<nuevo_tag>`: Asigna una nueva etiqueta a una imagen existente. Esto es útil si quieres, por ejemplo, preparar una imagen para subirla a un registro con un nombre específico (por ejemplo, `docker tag mi-app:latest mi-usuario/mi-app:1.0`).
- `docker push <nombre_imagen>:<tag>`: Sube una imagen a un registro de Docker. Necesitas estar autenticado en el registro (como Docker Hub) para que este comando funcione.

---

### Comandos para la creación de contenedores

Aunque no son directamente comandos de imágenes, son los que usas para interactuar con ellas, creando y manejando contenedores.

- `docker run <nombre_imagen>`: Es el comando más común. Realiza dos acciones en una:
  1.  Crea un nuevo contenedor a partir de la imagen.
  2.  Inicia el contenedor.
- `docker create <nombre_imagen>`: Crea un nuevo contenedor a partir de una imagen, pero **no lo inicia**. Esto te permite configurarlo antes de arrancarlo con `docker start`.
- `docker commit <id_contenedor> <nombre_nueva_imagen>`: Crea una nueva imagen a partir de un contenedor que está en ejecución. Este comando es útil para guardar el estado actual de un contenedor, pero no es la forma recomendada para el desarrollo regular. La mejor práctica es siempre modificar el `Dockerfile` y reconstruir.


---

Para que Docker inicie automáticamente un contenedor de Node.js al arrancar el sistema, debes configurar la política de reinicio (`restart policy`) al ejecutar el comando `docker run`.

La política de reinicio le dice a Docker qué hacer si el contenedor se detiene, ya sea por un error o porque el sistema se reinicia.

-----

### El comando clave: `docker run` con `--restart`

El comando que necesitas es el mismo que usas para iniciar tu contenedor, pero con el flag `--restart`.

```bash
docker run -d --restart unless-stopped --name mi-app-node mi-proyecto-node
```

#### Explicación del comando

  * `docker run`: El comando estándar para crear y ejecutar un contenedor.
  * `-d`: Ejecuta el contenedor en **modo desvinculado** (detached), es decir, en segundo plano. Esto es importante para que el contenedor siga corriendo incluso después de cerrar la terminal.
  * `--restart unless-stopped`: Esta es la parte crucial. Le dice a Docker que **siempre intente reiniciar el contenedor** cuando se detiene, a menos que el contenedor haya sido detenido manualmente por el usuario (`docker stop`). Esta es la política más recomendada para servicios que deben estar siempre activos.
  * `--name mi-app-node`: Asigna un nombre fácil de recordar al contenedor. Es una buena práctica para que puedas referirte a él más tarde con comandos como `docker stop mi-app-node` o `docker logs mi-app-node`.
  * `mi-proyecto-node`: El nombre de la imagen que quieres usar.

-----

### Opciones de la política de reinicio (`--restart`)

Puedes elegir entre varias políticas, dependiendo de tu caso de uso:

  * **`no`** (valor por defecto): El contenedor no se reiniciará automáticamente.
  * **`on-failure`**: El contenedor se reiniciará si se detiene con un código de error distinto de cero. No lo hará si el cierre es limpio.
  * **`always`**: Siempre reiniciará el contenedor, sin importar si fue detenido manualmente o por un error.
  * **`unless-stopped`** (recomendado): Reinicia el contenedor a menos que haya sido detenido manualmente. Esta es la opción más flexible y segura para la mayoría de los servicios, ya que te permite controlar manualmente cuándo quieres que se detenga de forma permanente.

Con el comando que te proporcioné, tu contenedor de Node.js se iniciará automáticamente cada vez que arranques tu sistema, garantizando que tu aplicación esté siempre disponible.
