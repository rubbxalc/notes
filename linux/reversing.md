# reversing

# Reversing

## Uso de radare2

### Escanear binario

```
radare2 myexec
[0x00400740]> aaa
```

### Listar funciones

```
0x00400740]> afl
```

### Seleccionar función

```
[0x00400740]> s main
```

### Ver ensamblador

```
[0x00400836]> pdf
```