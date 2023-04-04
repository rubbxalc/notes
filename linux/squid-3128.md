# Squid

## Añadir proxy al /etc/proxychains4.conf
```null
http 10.10.10.224 3128
```

### Pasar por su localhost
```null
http 10.10.10.224 3128
http 127.0.0.1 3128
```

### Lo mismo hacia otras IPs
```null
http 10.10.10.224 3128
http 127.0.0.1 3128
http 10.197.243.77 3128
```




