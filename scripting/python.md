# Python

## Definir función

```python
def def_handler(sig, frame):
    sys.exit(1)
```

## Variables globales

### Burp Proxy

```python
burp = {'http': 'http://localhost:8080'}
```

### Ctrl+C

```null
signal.signal(signal.SIGINT, def_handler)
```

## Requests

### Definir sesión

```null
s = requests.session()
```

### Obtener contenido por GET

```null
r = s.get(main_url)
```

### Post Data

```null
post_data = {
    '__VIEWSTATE': viewstate,
    '__EVENTVALIDATION': eventvalidation,
    'btnUpload': 'Upload'
}
```

## RE

### Expresiones regulares

```null
re.findall(r'id="__VIEWSTATE" value="(.*?)"', r.text)[0]
```

## Condicionales

### Texto no en respuesta

```null
if "Invalid File. Please try again" not in r.text:
    log.info("Extension %s is valid!!" % extension)
```

## Archivos

### Abrir

```null
f = open("/usr/share/wordlists/SecLists/Discovery/Web-Content/raft-large-extensions-lowercase.txt", "rb")
```

### Iterar por cada línea

```null
for extension in f.readlines():
    extension = extension.decode().strip()
    p1.status("Testing with %s" % extension)
    makeRequests(extension)
```

