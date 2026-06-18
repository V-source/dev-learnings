# Propagación de Errores

## Principio

Los errores viajan de **dentro hacia afuera** (de la capa más interna a la más externa). Cada capa solo debe capturar un error si va a **aportar valor**: dar más contexto, transformarlo, o realmente manejarlo.

Si una capa solo captura para loguear y re-lanzar, está duplicando ruido.

## Flujo correcto

```
Capa interna (más profunda)
  → captura el error
  → lo enriquece con contexto específico
  → lo re-lanza

Capas intermedias
  → NO capturan (dejan propagar)
  → o capturan solo si aportan nuevo contexto

Capa externa (caller final)
  → captura y maneja el error (responde al usuario, reintenta, etc.)
```

## Ejemplo concreto

```js
// ❌ Mal: la capa externa captura y loguea, pero la interna ya lo hizo
async #truncateTable(name) {
  try {
    await db.exec(`DELETE FROM ${name};`);
  } catch (error) {
    console.error(`Error truncating '${name}': ${error.message}`);
    throw error;
  }
}

async truncate(tableName) {
  try {
    await this.#taskLoopFor(names, this.#truncateTable);
  } catch (error) {
    console.error(`Error truncating table`);  // ← redundante, no aporta
    throw error;
  }
}
```

```js
// ✅ Bien: cada capa aporta o deja propagar
async #truncateTable(name) {
  try {
    await db.exec(`DELETE FROM ${name};`);
  } catch (error) {
    console.error(`Error truncating '${name}': ${error.message}`);
    throw error;
  }
}

async truncate(tableName) {
  const names = Array.isArray(tableName) ? tableName : [tableName];
  this.#sanitizeTableNames(names);
  await this.#taskLoopFor(names, this.#truncateTable);
  // No captura — deja que el error de #truncateTable se propague limpio
}

// El caller decide si captura o no
try {
  await db.tables.truncate("users");
} catch (error) {
  // Manejo real: responder al usuario, reintentar, etc.
}
```

## Las capas intermedias pueden ignorar el error

El error **salta** las capas que no lo capturan. No es obligatorio que todas las capas tengan try/catch.

```js
async function capaInterna() {
  throw new Error("falló");
}

async function capaIntermedia() {
  // No hay try/catch — el error pasa de largo
  await capaInterna();
}

async function capaSuperior() {
  try {
    await capaIntermedia();
  } catch (error) {
    console.error("Atrapado en capa superior:", error.message);
  }
}
```

Cada capa decide si captura o deja propagar. El error viaja hacia arriba hasta que alguien lo atrapa, o se convierte en **unhandled rejection** si nadie lo hace.

## ¿Cuándo capturar en capas superiores?

Cuando la capa superior **aporta valor** al capturar. Ejemplo clásico: una librería de logging visual (como `station-logs`) que necesita saber el estado de cada tarea para renderizar un árbol con estados success/error/skipped.

```js
// ❌ Mal: capturar en CADA capa sin motivo real
async #truncateTable(name) {
  try {
    await this.db.exec(`DELETE FROM ${name};`);
  } catch (error) {
    console.error(`Error truncating '${name}': ${error.message}`);
    throw error;
  }
}

async truncate(tableName) {
  try {
    await this.#taskLoopFor(names, this.#truncateTable);
  } catch (error) {
    console.error(`Error truncating`); // redundante
    throw error;
  }
}

async resetDatabase() {
  try {
    await this.truncate('users');
  } catch (error) {
    log.log('users', { state: 'skipped' }); // tercer log del mismo error
  }
}
```

```js
// ✅ Bien: capturar SOLO en la capa que necesita la info
async #truncateTable(name) {
  await this.db.exec(`DELETE FROM ${name};`);
  // No captura — deja propagar
}

async truncate(tableName) {
  const names = Array.isArray(tableName) ? tableName : [tableName];
  this.#sanitizeTableNames(names);
  await this.#taskLoopFor(names, this.#truncateTable);
  // No captura — deja propagar
}

async resetDatabase() {
  const log = new LogTree('Reset');
  log.setStations(['users', 'roles', 'cleanup']);

  try {
    log.log('users', { state: 'running' });
    await this.truncate('users');
    log.log('users', { state: 'success' });

    log.log('roles', { state: 'running' });
    await this.truncate('roles');
    log.log('roles', { state: 'success' });
  } catch (error) {
    // Única captura — aporta valor al marcar skipped en el árbol
    log.log('roles', { state: 'skipped' });
    log.log('cleanup', { state: 'skipped' });
  }

  log.getLogs();
}
```

## No todas las capas, solo las necesarias

No tenés que capturar en **todas** las capas ni en **una sola** específica. Capturás **donde tenga sentido hacerlo**.

| Capa | Motivo para capturar |
|------|----------------------|
| Interna | Contexto específico (nombre de tabla, query, etc.) |
| Intermedia | Generalmente NO captura, deja propagar |
| Orchestrador | Reportar estado visual, decidir si continuar |
| Caller final | Responder al usuario, reintentar, registrar |

Si ninguna capa necesita aportar valor, ninguna captura. El error llega igual a donde tenga que llegar.

## Regla de oro

> Capturá un error solo si vas a **agregar información** o **manejar la falla**. Si solo vas a loguear y re-lanzar, no captures.
