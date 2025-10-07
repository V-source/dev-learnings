
# ESTRUCTURA DE NOTIFICACIONES CON EXPO NOTIFICATIONS SDK.

Vamos a desglosar **cada propiedad** del tipo `ExpoPushMessage` (usado para enviar notificaciones con **Expo Notifications**), explicando **qué representa, qué tipo de valor acepta** y **para qué sirve en la práctica**.

---

### 🧩 Estructura general

```ts
export type ExpoPushMessage = {
  to: ExpoPushToken | ExpoPushToken[];
  data?: Record<string, unknown>;
  title?: string;
  subtitle?: string;
  body?: string;
  sound?: string | null | {
    critical?: boolean;
    name?: string | null;
    volume?: number;
  };
  ttl?: number;
  expiration?: number;
  priority?: 'default' | 'normal' | 'high';
  interruptionLevel?: 'active' | 'critical' | 'passive' | 'time-sensitive';
  badge?: number;
  channelId?: string;
  icon?: string;
  richContent?: {
    image?: string;
  };
  categoryId?: string;
  mutableContent?: boolean;
  _contentAvailable?: boolean;
};
```

---

## 🔹 Propiedades principales

### `to: ExpoPushToken | ExpoPushToken[]`

* **Qué es:** El o los tokens de dispositivos a los que se enviará la notificación.
* **Valor:** Una cadena o un arreglo de cadenas.

  ```ts
  to: "ExponentPushToken[xxxxxxxxxxxxxxxxxxxxxx]"
  ```

  o bien:

  ```ts
  to: [
    "ExponentPushToken[xxxxx1]",
    "ExponentPushToken[xxxxx2]"
  ]
  ```
* **Uso:** Siempre es **obligatorio**.

---

### `data?: Record<string, unknown>`

* **Qué es:** Datos adicionales que acompañan la notificación.
* **Valor:** Un objeto con pares clave-valor (solo se envían al cliente, no se muestran en pantalla).

  ```ts
  data: { route: "MessagesScreen", userId: 42 }
  ```
* **Uso:** Ideal para manejar lógica interna (por ejemplo, abrir una pantalla específica al pulsar la notificación).

---

### `title?: string`

* **Qué es:** El **título principal** de la notificación.

  ```ts
  title: "Nuevo mensaje recibido"
  ```

---

### `subtitle?: string`

* **Qué es:** Una **línea secundaria** que aparece debajo del título (solo iOS).

  ```ts
  subtitle: "De Juan Pérez"
  ```

---

### `body?: string`

* **Qué es:** El **contenido principal** visible de la notificación.

  ```ts
  body: "Tienes un nuevo mensaje en tu bandeja."
  ```

---

## 🔊 Sonido y alertas

### `sound?: string | null | { ... }`

* **Qué es:** Controla el sonido que se reproduce al recibir la notificación.
* **Valores posibles:**

  ```ts
  sound: "default" // reproduce el sonido predeterminado
  sound: null      // sin sonido
  ```

  o bien, configuración avanzada:

  ```ts
  sound: {
    critical: true,     // reproduce sonido incluso con el teléfono en silencio (solo iOS)
    name: "alert.wav",  // nombre del archivo de sonido
    volume: 0.8         // volumen (0.0 a 1.0)
  }
  ```

---

## ⏱️ Tiempo de vida y prioridad

### `ttl?: number`

* **Qué es:** "Time To Live", tiempo (en segundos) que el servidor mantiene la notificación si el dispositivo está desconectado.

  ```ts
  ttl: 3600 // 1 hora
  ```

---

### `expiration?: number`

* **Qué es:** Marca de tiempo UNIX (en segundos) cuando la notificación expira y deja de enviarse.

  ```ts
  expiration: Math.floor(Date.now() / 1000) + 86400 // expira en 1 día
  ```

---

### `priority?: 'default' | 'normal' | 'high'`

* **Qué es:** Controla la prioridad de entrega (principalmente en Android).

  * `default` → comportamiento estándar.
  * `normal` → entrega diferida, ahorro de batería.
  * `high` → entrega inmediata, incluso si el dispositivo está inactivo.

  ```ts
  priority: "high"
  ```

---

## ⚙️ Interrupciones y presentación

### `interruptionLevel?: 'active' | 'critical' | 'passive' | 'time-sensitive'`

* **Qué es:** Define cómo interrumpe la notificación al usuario (iOS 15+).

  * `passive`: No interrumpe.
  * `active`: Interrumpe normalmente.
  * `time-sensitive`: Pasa incluso si el modo “No molestar” está activo.
  * `critical`: Rompe todas las restricciones (requiere permiso especial).

  ```ts
  interruptionLevel: "time-sensitive"
  ```

---

### `badge?: number`

* **Qué es:** Número que aparece en el ícono de la app (solo iOS).

  ```ts
  badge: 3
  ```

---

### `channelId?: string`

* **Qué es:** Canal de notificaciones en Android (debe haberse creado previamente).

  ```ts
  channelId: "messages"
  ```

---

### `icon?: string`

* **Qué es:** Ruta o nombre del ícono personalizado (Android).

  ```ts
  icon: "@drawable/notification_icon"
  ```

---

### `richContent?: { image?: string }`

* **Qué es:** Añade contenido enriquecido (como una imagen) a la notificación.

  ```ts
  richContent: {
    image: "https://example.com/promo.jpg"
  }
  ```

---

### `categoryId?: string`

* **Qué es:** Identificador de una **categoría** de notificación con acciones asociadas.
  (Las categorías se definen dentro de la app con `Notifications.setNotificationCategoryAsync()`.)

  ```ts
  categoryId: "messageActions"
  ```

---

### `mutableContent?: boolean`

* **Qué es:** Permite modificar la notificación antes de mostrarla (usado con Notification Service Extensions en iOS).

  ```ts
  mutableContent: true
  ```

---

### `_contentAvailable?: boolean`

* **Qué es:** Indica que la notificación contiene datos silenciosos para ejecución en segundo plano (iOS).

  ```ts
  _contentAvailable: true
  ```

---

## 🔧 Ejemplo completo

```ts
const message: ExpoPushMessage = {
  to: "ExponentPushToken[xxxxxxxxxxxxxxxxxxxxx]",
  title: "Nuevo pedido recibido",
  body: "El cliente Juan Pérez ha realizado un nuevo pedido.",
  data: { orderId: 1234 },
  sound: "default",
  ttl: 3600,
  priority: "high",
  badge: 5,
  channelId: "pedidos",
  interruptionLevel: "time-sensitive",
  richContent: {
    image: "https://example.com/pedido.jpg"
  }
};
```

---

¿Quieres que te muestre **qué propiedades funcionan en Android, iOS o ambas** (en una tabla comparativa)?
Eso te ayudaría a saber qué usar dependiendo de tu plataforma.
