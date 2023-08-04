# FTP

## Conexión y listar archivos

```null
ftp 10.10.10.103
Connected to 10.10.10.103.
220 Microsoft FTP Service
Name (10.10.10.103:rubbx): anonymous
331 Anonymous access allowed, send identity (e-mail name) as password.
Password: 
230 User logged in.
Remote system type is Windows_NT.
ftp> dir
229 Entering Extended Passive Mode (|||52959|)
125 Data connection already open; Transfer starting.
226 Transfer complete.
ftp> 
```