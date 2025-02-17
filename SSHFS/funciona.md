# Instalar
```bash

sudo pacman -S sshfs
```
# Crear punto de montaje
```bash

mkdir ~/win-dev
```

**ejemplo**:

```bash

sshfs usuario@ip:/Users/Usuario/carpeta/ ~/win-dev/
```


# Automatizar con script personalizado
> [!NOTE]
> para ejecutar cuando quieras.

```bash

comando_pesonalizado() {
  expect -c "
    spawn sshfs usuario@ip:/Users/Usuario/carpeta/ ~/win-dev/
    expect \"password:\"
    send \"mi contrase;a\r\"
    expect eof
  "
}

```
