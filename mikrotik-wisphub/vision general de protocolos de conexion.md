Cuando hablamos de sistemas como Wisphub, que gestionan y controlan routers MikroTik, se utilizan una variedad de protocolos de conexión, cada uno con un propósito específico. Aquí te detallo los más relevantes:

# Protocolos de Gestión Remota:

* **SSH (Secure Shell):**
    * Este es un protocolo crucial para la administración remota segura. Permite a Wisphub conectarse a los routers MikroTik y ejecutar comandos de configuración de manera segura.
    * SSH cifra la comunicación, protegiendo las credenciales y los datos transmitidos.
* **API de MikroTik:**
    * MikroTik proporciona una API (Interfaz de Programación de Aplicaciones) que permite a sistemas externos, como Wisphub, interactuar con los routers de manera programática.
    * Wisphub utiliza esta API para automatizar tareas de configuración, supervisar el estado de los dispositivos y recopilar datos de rendimiento.
* **Winbox API:**
    * Winbox es la herramienta gráfica de administración propietaria de MikroTik. Los desarrolladores han ingeniado la manera de utilizar la logica de este programa mediante apis.

# Protocolos de Red Esenciales:

* **TCP/IP (Transmission Control Protocol/Internet Protocol):**
    * La base de la comunicación en Internet. Permite la conexión entre Wisphub y los routers MikroTik a través de la red.
* **HTTP/HTTPS (Hypertext Transfer Protocol/Hypertext Transfer Protocol Secure):**
    * Wisphub suele utilizar HTTP/HTTPS para la comunicación entre su interfaz web y los servidores, así como para la posible comunicación con APIs externas.
    * HTTPS garantiza la seguridad de la comunicación mediante cifrado.

# Otros Protocolos Relevantes:

* **SNMP (Simple Network Management Protocol):**
    * Utilizado para la supervisión y gestión de dispositivos de red. Wisphub puede utilizar SNMP para recopilar información sobre el estado y el rendimiento de los routers MikroTik.

# En resumen:

La combinación de SSH, la API de MikroTik y los protocolos TCP/IP y HTTP/HTTPS permite a sistemas como Wisphub administrar y controlar los routers MikroTik de manera eficiente y segura.
