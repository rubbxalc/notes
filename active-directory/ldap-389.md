# LDAP - 389

## Uso de Ldapsearch

### Listar namingcontexts
```null
ldapsearch -h 10.10.10.192 -x -s base namingcontexts
```

### Dumpear información

```null
ldapsearch -x -b "DC=megabank,DC=local" -H ldap://10.10.10.169:389
```

