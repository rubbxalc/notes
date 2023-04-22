# SMTP - 25

## Enviar correo
```null
swaks --to itsupport@outdated.htb --from rubbx@rubbx.com --body "http://10.10.14.2/" --header "Subject: Internal web app"
```

## Enviar con archivo

```null
sendEmail -f rubbx@test.com -t nico@megabank.local -u Pwned -m Pwned -s 10.10.10.77:25 -a file.rtf
```

## Enumeración de usuarios

```null
smtp-user-enum -M RCPT -U mails -t 10.10.10.77
```

