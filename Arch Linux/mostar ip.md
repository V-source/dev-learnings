```bash
ip addr show | grep 'inet ' | awk '{print $2}' | cut -d'/' -f1
```

**Explicación:**

* `ip addr show`: Este comando muestra toda la información de las interfaces de red de tu sistema.
* `grep 'inet '`: Filtra la salida del comando anterior para mostrar solo las líneas que contienen la dirección IP.
* `awk '{print $2}'`: Extrae el segundo campo de cada línea, que es la dirección IP.
* `cut -d'/' -f1`: Elimina la máscara de subred de la dirección IP.

Este comando te dará como resultado solo la dirección IP de tu máquina.
