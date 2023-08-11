# Bash

### Pseudocurl

```bash
function __curl() {  read proto server path <<<$(echo ${1//// })  DOC=/${path// //}  HOST=${server//:*}  PORT=${server//*:}  [[ x"${HOST}" == x"${PORT}" ]] && PORT=80  exec 3<>/dev/tcp/${HOST}/$PORT  echo -en "GET ${DOC} HTTP/1.0\r\nHost: ${HOST}\r\n\r\n" >&3  (while read line; do   [[ "$line" == $'\r' ]] && break  done && cat) <&3  exec 3>&-}
```

### HostDiscovery

```bash
#!/bin/bashfunction ctrl_c(){  exit 1}# Ctrl+Ctrap ctrl_c INTnetworks=(172.18.0 172.19.0)for network in ${networks[@]};do  for i in $(seq 1 254); do    timeout 1 bash -c "ping -c 1 $network.$i" &>/dev/null && echo "[+] HOST $network.$i - ACTIVE" &  done; waitdone
```

### PortDiscovery

```bash
#!/bin/bashfunction ctrl_c(){  exit 1}# Ctrl+Ctrap ctrl_c INTnetworks=(172.18.0.2 172.18.0.1 172.19.0.4 172.19.0.3 172.19.0.2 172.19.0.1)for network in ${networks[@]};do  for port in $(seq 1 65535); do    timeout 1 bash -c "echo '' > /dev/tcp/$network/$port" &>/dev/null && echo "[+] HOST $network:$port - ACTIVE" &  done; waitdone
```