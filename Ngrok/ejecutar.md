para ejecutar solo debes ejecutar el servidor y saber que puerto usa

por ejemplo
loclahost:5000
el puerto es 5000

asi que ejecutas


```bash
ngrok http 5000
```

esto debe darte una salida asi:

```bash

Forwarding    https://e7ff-2800-xxx-xxx.ngrok-free.app -> http://localhost:7689
```

o algo asi:

```bash

ngrok                                (Ctl+C to quit)

ngrok does not support a dynamic, color terminal UI on solaris.
Access the web interface for connection and tunnel status.

Version                       3.29.0
Region                        Auto (lowest latency) (auto)
Web Interface                 http://127.0.0.1:4040

```
si este es el caso debes ir a la url `http://127.0.0.1:4040` que es su administrador
ahi encontraras la url temporal que debes usar para compartir

algo asi: `https://e7ff-2800-xxx-xxx.ngrok-free.app`
