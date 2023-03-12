# Pivoting

## Escaneo ultragresivo con nmap
```null
seq 1 65535 | xargs -P 200 -I {} proxychains nmap -p{} -sT -Pn -v -n 10.197.243.77 -vvv 2>/dev/null | grep "tcp open"
```

