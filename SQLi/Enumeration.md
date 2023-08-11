# Enumeration

# Enumeration

## Uso de sqlcmd

### Enumeración de bases de datos

```
sqlcmd -U db_admin -P 'B1@hx31234567890' -S localhost -d streamio_backup -Q "SELECT name FROM master..sysdatabases;"
```

### Extracción de tablas

```
sqlcmd -U db_admin -P 'B1@hx31234567890' -S localhost -d streamio_backup -Q "SELECT name FROM streamio_backup..sysobjects WHERE xtype = 'U';"
```

### Extracción de columnas

```
sqlcmd -U db_admin -P 'B1@hx31234567890' -S localhost -d streamio_backup -Q "SELECT * from users;"
```

## Tipo mongodb

### Enumerar bases de datos

```
show dbs
```

### Extracción de tablas

```
use <DBS>
```

```
show collections
```

### Dumpear todos los datos para una tabla

```
db.<TABLE>.find()
```

### Modificar valores

```
db.<TABLE>.update( {"<COLUMN>" : "<VALUE>" }, {$set: {"<COLUMN>": "<VALUE>"} });
```