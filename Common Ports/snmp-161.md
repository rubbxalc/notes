# snmp-161

# SNMP

## Fuerza bruta Community String

```
onesixtyone -c /usr/share/wordlists/SecLists/Discovery/SNMP/common-snmp-community-strings-onesixtyone.txt 10.10.10.92
```

## Recolección de información

```
snmpbulkwalk -v2c -c public 10.10.10.92 > snmpbulkscan
```

## Extracción IPv6

```
cat snmpbulkscan | grep IP-MIB | grep -oP '".*?"' | tr -d '"' | grep "de" | tail -n 1
```