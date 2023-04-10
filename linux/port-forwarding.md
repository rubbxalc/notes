# Port Forwarding

## LPF SSH

```null
ssh marcus@10.10.10.238 -L 8443:localhost:8443
```

## Uso de CHISEL

### Servidor

```null
chisel server -p 1234 --reverse
```

### Cliente

```null
chisel.exe client 10.10.16.4:1234 R:socks
```

## Uso de socat

```null
socat TCP-LISTEN:1112,fork TCP:172.30.0.9:81
```

## Agregar rutas estáticas

```null
ip route add 172.20.0.0/24 dev tun9
```

