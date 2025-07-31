**Interfaces clave:**

  * `PictureInPictureWindow`: Representa la ventana flotante.
  * `PictureInPictureEvent`: Para manejar eventos relacionados.

**Métodos importantes para controlar el modo Picture-in-Picture:**

  * `HTMLVideoElement.requestPictureInPicture()`: Para entrar en el modo.
  * `Document.exitPictureInPicture()`: Para devolver el video a su posición original.

**Propiedades útiles:**

  * `Document.pictureInPictureEnabled`: Para verificar si el modo es compatible.
  * `Document.pictureInPictureElement`: Para identificar el elemento actualmente en la ventana flotante.

**Eventos importantes:**

  * `enterpictureinpicture`: Notifica cuando se entra en el modo Picture-in-Picture.
  * `leavepictureinpicture`: Notifica cuando se sale del modo Picture-in-Picture.
  * `resize`: Notifica sobre cambios en el tamaño de la ventana Picture-in-Picture.

**Ejemplo de código para alternar el modo Picture-in-Picture:**

```javascript
// Obtener el elemento de video
const video = document.querySelector('video');

// Obtener el botón para alternar Picture-in-Picture
const pipButton = document.querySelector('.toggle-pip-button');

// Comprobar si la API de Picture-in-Picture es compatible con el navegador
if (document.pictureInPictureEnabled) {
  pipButton.disabled = false; // Habilitar el botón si es compatible
}

// Función para alternar el modo Picture-in-Picture
pipButton.addEventListener('click', async () => {
  if (document.pictureInPictureElement) {
    // Si ya estamos en Picture-in-Picture, salir
    await document.exitPictureInPicture();
  } else {
    // Si no estamos en Picture-in-Picture, solicitar entrar
    await video.requestPictureInPicture();
  }
});

// Estilizando el video cuando está en Picture-in-Picture (ejemplo CSS)
// video:picture-in-picture {
//   border: 2px solid blue;
// }
```
