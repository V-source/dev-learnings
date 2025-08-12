### Conceptos Clave de Diseño de Sistemas

* **Arquitectura Cliente-Servidor:** Un modelo donde un cliente (como un navegador) solicita recursos a un servidor, que se los entrega. Es la base de la mayoría de las aplicaciones web.

* **Dirección IP:** Una cadena única de números que identifica un dispositivo en una red, similar a una dirección física para tu casa en internet.

* **DNS (Domain Name System):** Un sistema que traduce nombres de dominio legibles por humanos (como `google.com`) a direcciones IP, que las computadoras pueden entender.

* **Proxy / Reverse Proxy:** Un **proxy** reenvía las peticiones de un cliente a internet, mientras que un **proxy inverso** reenvía las peticiones de internet a los servidores internos de una red.

* **Latencia:** El tiempo que tarda una petición en ir desde el cliente hasta el servidor y volver. Una latencia baja significa un sistema más rápido.

* **HTTP/HTTPS:** Protocolos para transferir datos a través de la web. **HTTPS** es la versión segura y encriptada.

* **APIs (Application Programming Interfaces):** Conjuntos de reglas que permiten a diferentes sistemas comunicarse entre sí y compartir datos.

* **REST API:** Un estilo popular para diseñar APIs usando métodos HTTP estándar (GET, POST, PUT, DELETE).

* **GraphQL:** Una alternativa a REST que permite a los clientes especificar exactamente los datos que necesitan, evitando la sobre-obtención de datos.

* **Bases de Datos:** Sistemas que almacenan datos de forma estructurada o no estructurada para que puedan ser consultados y actualizados.

* **SQL vs NoSQL:** **SQL** es un modelo de base de datos relacional (tablas con un esquema fijo), mientras que **NoSQL** es no relacional (documentos, clave-valor, etc.) y más flexible.

* **Escalabilidad Vertical vs. Horizontal:** La **escalabilidad vertical** consiste en aumentar la potencia de un servidor (más CPU, RAM). La **escalabilidad horizontal** consiste en añadir más servidores para manejar el tráfico en paralelo.

* **Balanceadores de Carga (Load Balancers):** Dispositivos o software que distribuyen el tráfico de red entre varios servidores para optimizar el rendimiento y evitar la sobrecarga.

* **Indexación de Bases de Datos:** Una técnica que acelera la recuperación de datos en una base de datos al crear una estructura de búsqueda rápida.

* **Replicación:** El proceso de copiar datos a través de múltiples servidores de bases de datos para garantizar la fiabilidad y la alta disponibilidad.

* **Sharding:** La división de una base de datos en fragmentos más pequeños (shards) para que cada servidor maneje solo una parte de los datos.

* **Caching:** Almacenar datos a los que se accede con frecuencia en la memoria para evitar consultas lentas o cálculos repetidos.

* **Teorema CAP:** Un principio del diseño de sistemas distribuidos que establece que es imposible garantizar simultáneamente consistencia, disponibilidad y tolerancia a particiones.

* **CDN (Content Delivery Network):** Una red de servidores distribuidos geográficamente que almacenan copias del contenido de un sitio web para entregarlo a los usuarios de forma más rápida.

* **WebSockets:** Un protocolo que permite una comunicación bidireccional en tiempo real entre un cliente y un servidor, a diferencia del HTTP, que es unidireccional.

* **Microservicios:** Un enfoque arquitectónico donde una aplicación se descompone en pequeños servicios independientes y débilmente acoplados.

* **Colas de Mensajes (Message Queues):** Sistemas que permiten la comunicación asíncrona entre diferentes componentes de una aplicación.

* **Idempotencia:** La propiedad de una operación en la que, si se ejecuta varias veces, produce el mismo resultado que si se hubiera ejecutado una sola vez.


---

[System Design was HARD until I Learned these 30 Concepts](https://www.youtube.com/watch?v=s9Qh9fWeOAk) explica 30 de los conceptos más importantes de diseño de sistemas.
