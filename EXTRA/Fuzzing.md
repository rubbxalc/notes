# Fuzzing

# Fuzzing

## Uso de gobuster para descubrir rutas y archivos

```
gobuster dir -u http://10.10.10.25:8000 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 100 -x html,php,txt
```