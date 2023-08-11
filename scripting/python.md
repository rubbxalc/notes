# Python

## Definir función

```python
def def_handler(sig, frame):
```

## Variables globales

### Burp Proxy

```python
burp = {'http': 'http://localhost:8080'}
```

### Ctrl+C

```
signal.signal(signal.SIGINT, def_handler)
```

## Requests

### Definir sesión

```
s = requests.session()
```

### Obtener contenido por GET

```
r = s.get(main_url)
```

### Post Data

```
post_data = {
    '__VIEWSTATE': viewstate,
    '__EVENTVALIDATION': eventvalidation,
    'btnUpload': 'Upload'
}
```

## RE

### Expresiones regulares

```
re.findall(r'id="__VIEWSTATE" value="(.*?)"', r.text)[0]
```

## Condicionales

### Texto no en respuesta

```
if "Invalid File. Please try again" not in r.text:
    log.info("Extension %s is valid!!" % extension)
```

## Archivos

### Abrir

```
f = open("/usr/share/wordlists/SecLists/Discovery/Web-Content/raft-large-extensions-lowercase.txt", "rb")
```

### Iterar por cada línea

```
for extension in f.readlines():
    extension = extension.decode().strip()
    p1.status("Testing with %s" % extension)
    makeRequests(extension)
```