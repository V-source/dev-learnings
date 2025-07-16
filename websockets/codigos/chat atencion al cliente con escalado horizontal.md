# Solución para Chat Escalable sin Redis ni Servicios Externos


## **Arquitectura Basada en Node.js Puro**

### **1. Almacenamiento de Conexiones en Memoria (Optimizado)**

```javascript
// connectionManager.js
class ConnectionManager {
  constructor() {
    this.connections = new Map(); // userId -> socketId
    this.operatorConnections = new Set(); // socketIds de operadores
    this.clientRooms = new Map(); // clientId -> roomId
  }

  addConnection(userId, socketId, role) {
    this.connections.set(userId, socketId);
    if (role === 'operator') {
      this.operatorConnections.add(socketId);
    }
  }

  getConnection(userId) {
    return this.connections.get(userId);
  }

  createPrivateRoom(clientId, operatorId) {
    const roomId = `priv_${clientId}_${operatorId}`;
    this.clientRooms.set(clientId, roomId);
    return roomId;
  }

  // ... otros métodos útiles
}

module.exports = new ConnectionManager();
```

### **2. Servidor con Cluster Nativo de Node.js**

```javascript
// server.js
const cluster = require('cluster');
const numCPUs = require('os').cpus().length;
const connectionManager = require('./connectionManager');

if (cluster.isMaster) {
  console.log(`Master ${process.pid} is running`);
  
  // Fork workers
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }
  
  cluster.on('exit', (worker) => {
    console.log(`Worker ${worker.process.pid} died`);
    cluster.fork(); // Auto-recovery
  });
} else {
  const express = require('express');
  const http = require('http');
  const socketIo = require('socket.io');
  
  const app = express();
  const server = http.createServer(app);
  const io = socketIo(server, {
    cors: {
      origin: "*",
      methods: ["GET", "POST"]
    }
  });

  // Manejo de conexiones
  io.on('connection', (socket) => {
    const { userId, role } = socket.handshake.auth;
    
    // Registrar conexión
    connectionManager.addConnection(userId, socket.id, role);
    
    // Lógica para clientes
    if (role === 'client') {
      handleClientConnection(socket, userId);
    }
    
    // Lógica para operadores
    if (role === 'operator') {
      handleOperatorConnection(socket);
    }
    
    socket.on('disconnect', () => {
      connectionManager.removeConnection(userId, socket.id, role);
    });
  });

  server.listen(3000, () => {
    console.log(`Worker ${process.pid} listening on port 3000`);
  });
}
```

### **3. Estrategia de Comunicación entre Workers**

Como no puedes usar Redis, implementaremos un sistema de mensajería básico entre workers usando el módulo `cluster`:

```javascript
// interWorkerComm.js
const cluster = require('cluster');

function broadcastToWorkers(message) {
  if (cluster.isMaster) return;
  
  process.send(message); // Enviar al proceso maestro
}

// En el master:
if (cluster.isMaster) {
  for (const id in cluster.workers) {
    cluster.workers[id].on('message', (msg) => {
      // Reenviar a todos los workers
      for (const workerId in cluster.workers) {
        if (workerId !== id) {
          cluster.workers[workerId].send(msg);
        }
      }
    });
  }
}

// En los workers:
if (cluster.isWorker) {
  process.on('message', (msg) => {
    // Manejar mensajes de otros workers
    switch(msg.type) {
      case 'newMessage':
        // ... lógica para mensajes
        break;
      case 'connectionUpdate':
        // ... actualizar conexiones
        break;
    }
  });
}

module.exports = { broadcastToWorkers };
```

## **Técnicas de Optimización de Memoria**

Para manejar miles de conexiones sin Redis:

### **1. Uso eficiente de estructuras de datos**

```javascript
// Usar WeakMap para datos temporales
const sessionData = new WeakMap();

// Almacenamiento optimizado de conexiones
class ConnectionStore {
  constructor() {
    this.connections = new Map();
    this.clientIndex = new Map(); // socketId -> userId
  }
  
  add(userId, socket) {
    this.connections.set(userId, socket);
    this.clientIndex.set(socket.id, userId);
    sessionData.set(socket, { 
      connectedAt: Date.now(),
      lastActivity: Date.now()
    });
  }
  
  // ... otros métodos
}
```

### **2. Limpieza periódica de conexiones inactivas**

```javascript
setInterval(() => {
  const now = Date.now();
  connectionManager.connections.forEach((socket, userId) => {
    const data = sessionData.get(socket);
    if (now - data.lastActivity > 30 * 60 * 1000) { // 30 mins inactivo
      socket.disconnect();
      connectionManager.removeConnection(userId);
    }
  });
}, 5 * 60 * 1000); // Cada 5 minutos
```

## **Solución Completa para Mensajería Privada**

```javascript
// chatHandler.js
const connectionManager = require('./connectionManager');

module.exports = {
  handleClientMessage(socket, message) {
    const { userId } = socket.handshake.auth;
    const operatorId = findAvailableOperator(); // Lógica para asignar operador
    
    if (!operatorId) {
      socket.emit('error', 'No hay operadores disponibles');
      return;
    }
    
    const roomId = connectionManager.createPrivateRoom(userId, operatorId);
    socket.join(roomId);
    
    const operatorSocketId = connectionManager.getConnection(operatorId);
    if (operatorSocketId) {
      socket.to(roomId).emit('private_message', {
        from: userId,
        message,
        timestamp: Date.now()
      });
    } else {
      // Guardar en DB para cuando el operador se conecte
      savePendingMessage(userId, operatorId, message);
    }
  },
  
  handleOperatorMessage(socket, { clientId, message }) {
    const roomId = connectionManager.getRoomForClient(clientId);
    if (roomId) {
      socket.to(roomId).emit('private_message', {
        from: 'operator',
        message,
        timestamp: Date.now()
      });
    }
  }
};
```

## **Recomendaciones para Escalabilidad Futura**

1. **Implementa conexiones persistentes**:
   - Usa `socket.io` con transporte WebSocket primario
   - Configura timeouts adecuados

2. **Optimiza el uso de memoria**:
   - Limita el historial de chat en memoria
   - Usa streaming para chats largos

3. **Estrategia de agrupación**:
   - Agrupa clientes por tipo/tema de soporte
   - Asigna operadores especializados

4. **Monitoreo obligatorio**:
   ```bash
   # Instalar herramientas de monitoreo
   npm install clinic autocannon -g
   
   # Analizar rendimiento
   clinic doctor -- node server.js
   ```

## **Tabla de Capacidad Estimada**

| Componente          | Capacidad (por worker) | Con 4 CPUs | Notas                     |
|---------------------|-----------------------|-----------|---------------------------|
| Conexiones activas  | ~2,500                | ~10,000   | Depende de RAM disponible |
| Mensajes/minuto     | ~5,000                | ~20,000   | Optimizando código        |
| Memoria utilizada   | ~500MB                | ~2GB      | Por worker                |

Esta solución te permitirá:
- Manejar tu carga actual de ~1,000 clientes diarios
- Escalar hasta ~20,000 clientes añadiendo más workers
- Mantener la privacidad de cada conversación
- No depender de servicios externos

El cuello de botella será la memoria RAM disponible. Para 20,000+ clientes, eventualmente necesitarás considerar Redis u otra solución externa, pero esta arquitectura te dará tiempo para crecer hasta ese punto.
