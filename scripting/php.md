# PHP

## Listar contenido directorio

```
<?php print_r(scandir(".")); ?>
```

## Obtener contenido de un archivo

```
<?php echo file_get_contents("../web.config.old"); ?>
```

## Introducir contenido en directorio

```null <?php file_put_contents(“test.txt”,base64_decode(“Q3VhbHF1aWVyY29zYQo=”)); echo ‘Test’?>