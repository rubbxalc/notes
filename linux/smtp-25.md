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
Starting smtp-user-enum v1.2 ( http://pentestmonkey.net/tools/smtp-user-enum )

 ----------------------------------------------------------
|                   Scan Information                       |
 ----------------------------------------------------------

Mode ..................... RCPT
Worker Processes ......... 5
Usernames file ........... mails
Target count ............. 1
Username count ........... 1
Target TCP port .......... 25
Query timeout ............ 5 secs
Target domain ............ 

######## Scan started at Mon Feb 27 18:30:38 2023 #########
10.10.10.77: nico@megabank.com exists
######## Scan completed at Mon Feb 27 18:30:39 2023 #########
1 results.

1 queries in 1 seconds (1.0 queries / sec)
```

