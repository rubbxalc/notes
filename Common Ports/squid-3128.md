# squid-3128

# Squid

## Añadir proxy al /etc/proxychains4.conf

```
http 10.10.10.224 3128
```

### Pasar por su localhost

```
http 10.10.10.224 3128
http 127.0.0.1 3128
```

### Lo mismo hacia otras IPs

```
http 10.10.10.224 3128
http 127.0.0.1 3128
http 10.197.243.77 3128
```