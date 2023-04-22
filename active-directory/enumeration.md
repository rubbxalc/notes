# Enumeration

## Macros

```null
olevba -c CurrencyVolumeReport.xlsm
```

## Obtención IPv6 abusando de MSSRPC

```null
IOXIDResolver.py -t 10.10.10.52
[*] Retrieving network interface of 10.10.10.52
Address: mantis
Address: 10.10.10.52
Address: dead:beef::add2:50fb:c659:f2ac
```

## Creación de diccionarios de usuarios

```null
python3 /opt/SprayingToolkit/spindrift.py users --format "{f}.{last}" > bruteusers
python3 /opt/SprayingToolkit/spindrift.py users --format "{f}{last}" >> bruteusers
python3 /opt/SprayingToolkit/spindrift.py users --format "{first}{last}" >> bruteusers
python3 /opt/SprayingToolkit/spindrift.py users --format "{first}.{l}" >> bruteusers
python3 /opt/SprayingToolkit/spindrift.py users --format "{first}.{last}" >> bruteusers
```