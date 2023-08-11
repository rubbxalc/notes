# port-forwarding

# Port Forwarding

## LPF SSH

```
ssh marcus@10.10.10.238 -L 8443:localhost:8443
```

## Uso de CHISEL

### Servidor

```
chisel server -p 1234 --reverse
```

### Cliente

```
chisel.exe client 10.10.16.4:1234 R:socks
```

## Uso de socat

```
socat TCP-LISTEN:1112,fork TCP:172.30.0.9:81
```

## Agregar rutas estáticas

```
ip route add 172.20.0.0/24 dev tun9
```