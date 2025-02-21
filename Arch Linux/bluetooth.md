```bash
sudo pacman -S bluez bluez-utils
```

```bash
sudo systemctl start bluetooth.service
```

```bash
sudo systemctl enable bluetooth.service
```

```bash
bluetoothctl
# imprime:
[bluetooth]# Agent registered
[bluetooth]# 
```


```bash
[bluetooth]# power on
```

```bash
sudo modprobe btusb
```

> [!FIX]
> Driver Bluetooth

