# wifi
1. Lista dispositivos conectados: adb devices (anota el ID del teléfono, ej. ABC123DEF).
2. Especifica el dispositivo: adb -s <device_id> tcpip 5555 (reemplaza <device_id>).
3. Conecta por Wi-Fi: adb connect <ip_del_telefono>:5555 (usa IP del teléfono en ajustes > Wi-Fi).

**2da parte**

1. Desconéctalos primero: adb disconnect 192.168.0.26:38475 && adb disconnect 192.168.0.94:43131
2. En el teléfono correspondiente, conecta por USB temporalmente, ejecuta adb tcpip 5555 (especifica ID si hay múltiples: adb -s <id> tcpip 5555)
3. Desconecta el USB, ejecuta adb connect <ip_del_telefono>:5555 (usa IP exacta del teléfono en Wi-Fi).
4. Verifica: adb devices (debe mostrar online).
5. Abre la app de cámara en el teléfono.
6. Ejecuta scrcpy -s <ip>:5555 --fullscreen para verla en la PC. Repite para el otro IP.

alternativa

`scrcpy --video-source=camera --camera-size=1920x1080 --no-audio --tcpip -e`

`scrcpy --video-source=camera --camera-size=1920x1080 --camera-facing=front  --tcpip -s 192.168.0.94:5555 --no-audio`

luego de un rato debes ejecutar
`adb disconnect 192.168.0.94:5555 && adb connect 192.168.0.94:5555`

# usb
**camara trasera**
`scrcpy --video-source=camera --camera-size=1920x1080 --no-audio -d`
**camara delantera**
`scrcpy --video-source=camera --camera-size=1920x1080 --camera-facing=front --no-audio -d`
**camara delantera en vertical**
`scrcpy --video-source=camera --camera-size=1920x1080 --camera-facing=front --no-audio -d --orientation=270`


`scrcpy --video-source=camera --camera-size=1920x1080 --camera-facing=front --no-audio -d --orientation=270`
