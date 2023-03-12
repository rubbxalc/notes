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

## Uso de dnsenum

### Fuzzing de directorios

```null
dnsenum --dnsserver 10.10.10.224 --threads 30 -f /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt realcorp.htb
```