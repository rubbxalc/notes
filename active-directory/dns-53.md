# DNS - 53

## Uso de dig

### Consulta DNS Simple
```null
dig @10.10.10.192 blackfield.local
```

### Enumeración nameservers

```null
dig @10.10.10.192 blackfield.local ns
```

### Enumeración servidores de correo
```null
dig @10.10.10.192 blackfield.local ns
```

### Ataque de transferencia de zona
```null
dig axfr @10.10.10.192 blackfield.local
```

