# SNMP

## Fuerza bruta Community String

```null
onesixtyone -c /usr/share/wordlists/SecLists/Discovery/SNMP/common-snmp-community-strings-onesixtyone.txt 10.10.10.92
```

## Recolección de información

```null
snmpbulkwalk -v2c -c public 10.10.10.92 > snmpbulkscan
```

## Extracción IPv6

```null
cat snmpbulkscan | grep IP-MIB | grep -oP '".*?"' | tr -d '"' | grep "de" | tail -n 1
```

