Aquí tienes una **plantilla universal y reutilizable** del **patrón de composición en JavaScript**, lista para usar en proyectos reales (Express, Node, front-end, etc.).
La acompaño con explicaciones y un ejemplo aplicado 👇

---

## 🧩 PLANTILLA UNIVERSAL — “Composición Modular”

```js
// ===========================
// 🧱 1. BLOQUES DE COMPORTAMIENTO
// ===========================

// Cada bloque define una capacidad reutilizable (función pura que devuelve métodos)
const canLog = (entity) => ({
  log: (message) => console.log(`[${entity.name}] ${message}`)
});

const canSave = (entity) => ({
  save: () => console.log(`💾 Guardando ${entity.name} en base de datos...`)
});

const canNotify = (entity) => ({
  notify: (msg) => console.log(`🔔 Notificación para ${entity.name}: ${msg}`)
});

// ===========================
// 🧬 2. BASE / ESTADO INICIAL
// ===========================

const createEntity = (name, type = "generic") => ({
  id: Math.random().toString(36).slice(2),
  name,
  type,
  createdAt: new Date()
});

// ===========================
// 🧩 3. FACTORÍA DE COMPOSICIÓN
// ===========================

// La función combina el estado base con los módulos necesarios.
function composeEntity(name, type, ...behaviors) {
  const entity = createEntity(name, type);
  return Object.assign(
    {},
    entity,
    ...behaviors.map((behavior) => behavior(entity))
  );
}

// ===========================
// 🧠 4. CASOS DE USO REALES
// ===========================

// 📦 Usuario del sistema
const user = composeEntity("Elvis", "user", canLog, canNotify);
user.log("Inicio de sesión exitoso.");
user.notify("Tienes 3 nuevos mensajes.");

// 🛒 Pedido (Order)
const order = composeEntity("Pedido #879", "order", canLog, canSave);
order.log("Pedido en proceso...");
order.save();

// ⚙️ Servicio backend (Express, cron, etc.)
const service = composeEntity("EmailService", "system", canLog, canNotify);
service.log("Servicio iniciado correctamente.");
service.notify("Correo enviado a todos los usuarios.");
```

---

## 🧱 ESTRUCTURA GENERAL (conceptualmente)

```
[Módulos reutilizables]  → capacidades (canX)
[Base o estado]          → datos comunes (id, name, etc.)
[Función compositora]    → une base + módulos
[Objeto final]           → entidad con capacidades dinámicas
```

---

## ⚡️ 5. EXTENSIÓN Y ESCALABILIDAD

Puedes **crear comportamientos más avanzados** con dependencias, configuraciones o asincronía:

```js
const canFetchData = (entity) => ({
  fetch: async (url) => {
    const res = await fetch(url);
    const data = await res.json();
    console.log(`📡 ${entity.name} recibió ${data.length} registros`);
    return data;
  },
});

const apiClient = composeEntity("API Client", "service", canLog, canFetchData);

(async () => {
  apiClient.log("Iniciando fetch...");
  await apiClient.fetch("https://jsonplaceholder.typicode.com/posts");
})();
```

---

## 🧠 6. VENTAJAS CLAVE

| Ventaja                   | Descripción                                                        |
| ------------------------- | ------------------------------------------------------------------ |
| 🧩 Modularidad            | Cada capacidad vive aislada y puede combinarse libremente          |
| 🔁 Reutilización          | No dependes de herencias ni clases base                            |
| ⚙️ Flexibilidad           | Puedes “inyectar” solo lo necesario                                |
| 🚫 Sin rigidez jerárquica | Evitas problemas del árbol de herencia (“el infierno del extends”) |
| 🧪 Testing fácil          | Cada módulo es una función pura, fácil de probar                   |

---

## 💡 7. Plantilla base lista para copiar

```js
// 🧩 Patrón de composición - Plantilla base
const createEntity = (name) => ({ name });

// Comportamientos reutilizables
const canSpeak = (e) => ({ speak: () => console.log(`${e.name} habla`) });
const canMove = (e) => ({ move: () => console.log(`${e.name} se mueve`) });

// Factory compositora
const compose = (base, ...traits) =>
  Object.assign({}, base, ...traits.map((t) => t(base)));

// Objeto final
const robot = compose(createEntity("R2D2"), canSpeak, canMove);
robot.speak();
robot.move();
```

---

¿Quieres que te deje **una versión avanzada** de esta plantilla aplicada a un entorno **Express.js** (por ejemplo, componiendo middlewares o servicios con capacidades como `canCache`, `canLog`, `canHandleErrors`)?
Sería una demostración perfecta del patrón en backend real.
