estructura:
```
project/
    db/
        mongoose.js
    utils/
        logger.js
    logs/
        mongo.log
    index.js
    .env
```


```javascript utils/logger.js

const { createLogger, format, trasnports } = require('winston')

const logger = createLogger({
    level: 'info',
    format: format.combine(
        format.timestamp({format: `YYY-MM-DD HH:mm:ss`}),
        format.printf(({timestam, level, message}) => `[${timestam}] ${level.toUpperCase()}: ${message}`)
    ),
    transports: [
        new transports.Console(),
        new trasnports.File({filename: 'logs/mongo.log', level: 'info'})
    ]
})

module.exports = logger;
```


```javascript db/mongoose.js

const mongoose = require('mongoose');
const logger = require('../utils/logger.js');
require('dotenv').config();

const MONGO_URI = process.env.MONGO_URI;
const RETRY_INTERVAL_MS = 5000;

let isConnectedBefore = false;

function connectWithRetry() {
    logger.info('[MongoDB] intentando conectar...');

    mongoose.connect(MONGO_URI, {
        serverSelectionTimeOutMS: 10000,
    }).catch(err => {
            logger.error(`[MongoDB] error ne conexion inicial: ${err.message}`)
        })
}

mongoose.connection.on('connected', () => {
    isConnectedBefore = true;
    logger.info('[MongoDB] Conectado exitosamente.')

})

mogoose.connection.on('error', (err) => {
    logger.error(`[MongoDB] Error en la conexion: ${err.message}`)
})

mongoose.connection.on('disconected', () => {
    logger.info('[MongoDB] Desconectado.')
    if(!isConnectedBefore) {
        setTimeout(connectWithRetry, RETRY_INTERVAL_MS)
    }
})


mongoose.connection.on('reconnected', () => {
    logger.info('[MongoDB] Reconectado.')
})
mongoose.connection.on('timeout', () => {
    logger.warn('[MongoDB] Timeout de conexion.')
})

function initMongoConnection() {
    connectWithRetry()
}

module.exports = {
    initMongoConnection,
    mongoose,
}
```

```javascript index.js

const { initMongoConnection} = require('./db/mongoose.js')

initMongoConnection()


```
