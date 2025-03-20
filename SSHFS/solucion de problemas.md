> [!bug] no mantiene la conexion activa

> [!code]
```bash
  expect -c "
    spawn ssh sucrenet@10.8.0.134
    expect \"password:\"
    send \"********\r\"
    expect eof
  "
```

solucion: cambiar `expect eof` por `interact`

El problema con el script radica en la última línea: `expect eof`. La función `expect eof` está diseñada para esperar el final del flujo de datos (end-of-file), lo que generalmente significa que la conexión SSH se cierra inmediatamente después de que se ejecuta el comando.

Aquí te explico por qué y cómo puedes solucionarlo:

**Entendiendo `expect eof`**

* **Propósito**: `expect eof` se utiliza para situaciones donde esperas que el servidor cierre la conexión después de enviar su respuesta. Es útil para automatizar la ejecución de comandos únicos y obtener el resultado.
* **Problema**: En tu caso, quieres mantener la conexión SSH abierta para interactuar con el servidor. Al usar `expect eof`, le estás diciendo al script que espere a que la conexión se cierre, lo que ocurre inmediatamente después de que el servidor te autentica y te da acceso al shell.

**Cómo mantener la conexión abierta**

Para mantener la conexión SSH abierta, necesitas eliminar la línea `expect eof` y, en su lugar, utilizar `interact`. La función `interact` transfiere el control de la sesión al usuario, permitiéndote interactuar directamente con el shell remoto.

Aquí tienes el script corregido:

```tcl
spawn ssh sucrenet@10.8.0.134
expect "password:"
send "********\r"
interact
```

**Explicación de la corrección:**

* **`interact`**: Esta función toma el control de la sesión y te permite enviar comandos al servidor y recibir respuestas como si estuvieras en una sesión SSH normal.
* **Comportamiento**: Con `interact`, la conexión SSH permanecerá abierta hasta que la cierres manualmente (por ejemplo, escribiendo `exit` en el shell remoto).

**Consideraciones adicionales:**

* **Automatización de comandos**: Si quieres automatizar la ejecución de comandos específicos después de la autenticación, puedes enviar los comandos usando `send` antes de llamar a `interact`. Por ejemplo:

```tcl
spawn ssh sucrenet@10.8.0.134
expect "password:"
send "********\r"
expect "$ " ;# espera el prompt
send "ls -l\r" ;# envia el comando ls -l
expect "$ " ;# espera el prompt
interact
```

* **Manejo de prompts**: Es importante tener en cuenta que el prompt del shell remoto puede variar. Si estás automatizando la ejecución de comandos, asegúrate de que tu script `expect` el prompt correcto antes de enviar cada comando.

Al reemplazar `expect eof` con `interact`, podrás mantener la conexión SSH abierta y interactuar con el servidor de forma continua.
