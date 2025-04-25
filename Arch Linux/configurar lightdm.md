ruta del archivo

```bash
/etc/lightdm/lightdm.conf
```

Los greeters de LightDM se encuentran principalmente en dos ubicaciones principales en el sistema:

- `/usr/share/xgreeters/` - Aquí se almacenan los greeters instalados globalmente
- `/etc/lightdm/lightdm.conf` - Este es el archivo de configuración principal que especifica qué greeter se está utilizando

### Directorios Principales

* `/usr/share/xgreeters/` - Contiene los archivos ejecutables de los greeters instalados

- Por ejemplo, si estás usando lightdm-gtk-greeter, aquí encontrarás el archivo ejecutable
- Este directorio es de solo lectura para usuarios normales

* `/etc/lightdm/lightdm.conf` - Este archivo contiene la configuración principal

- Aquí defines qué greeter quieres usar mediante la línea `greeter-session=`
- Requiere privilegios de superusuario para modificarlo

### Verificación de tu Greeter Actual

Para saber exactamente qué greeter estás usando, puedes:

1. Revisar el contenido de `/etc/lightdm/lightdm.conf`
2. Buscar la línea `greeter-session=`
3. El valor después del signo = indica el nombre de tu greeter actual


luego de realizar algun cambiio es recomendable reiniciar el sistema, de esta manera te aseguras de que tome todos los cambios.
