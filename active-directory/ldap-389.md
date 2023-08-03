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

### Enumeración general (Ver en HTML)

```null
ldapdomaindump -u 'intelligence.htb\Tiffany.Molina' -p 'NewIntelligenceCorpUser9876' 10.10.10.248
[*] Connecting to host...
[*] Binding to host
[+] Bind OK
[*] Starting domain dump
[+] Domain dump finished
```

