Puede haber ocasiones en las que aterricemos en un sistema con un shell limitado y Python no esté instalado. En estos casos, es bueno saber que podríamos usar varios métodos diferentes para generar un shell interactivo. Examinemos algunos de ellos.

## /bin/sh -i
Este comando ejecutará el intérprete de shell especificado en la ruta en modo interactivo ( `-i`).
```js
/bin/sh -i
sh: no job control in this shell
sh-4.2$
```

## Perl
Si el lenguaje de programación [Perl](https://www.perl.org) está presente en el sistema, estos comandos ejecutarán el intérprete de shell especificado.
```js
perl —e 'exec "/bin/sh";'
```

```js
perl: exec "/bin/sh";
```
El comando directamente arriba debe ejecutarse desde un script.

## Ruby
Si el lenguaje de programación [Ruby](https://www.ruby-lang.org/en/) está presente en el sistema, este comando ejecutará el intérprete de shell especificado:
```js
ruby: exec "/bin/sh"
```
El comando directamente arriba debe ejecutarse desde un script.

## lua
Si el lenguaje de programación [Lua](https://www.lua.org) está presente en el sistema, podemos utilizar el `os.execute` método para ejecutar el intérprete de shell especificado usando el comando completo a continuación:
```js
lua: os.execute('/bin/sh')
```

## AWK

[AWK](https://man7.org/linux/man-pages/man1/awk.1p.html) es un lenguaje de procesamiento y escaneo de patrones similar a C presente en la mayoría de los sistemas basados ​​en UNIX/Linux, ampliamente utilizado por desarrolladores y administradores de sistemas para generar informes. También se puede utilizar para generar un caparazón interactivo.
```js
awk 'BEGIN {system("/bin/sh")}'
```

## Find
[Buscar](https://man7.org/linux/man-pages/man1/find.1.html) es un comando presente en la mayoría de los sistemas Unix/Linux y se utiliza ampliamente para buscar y revisar archivos y directorios utilizando varios criterios. También se puede utilizar para ejecutar aplicaciones e invocar un intérprete de shell.
```js
find / -name nameoffile -exec /bin/awk 'BEGIN {system("/bin/sh")}' \;
```
Este uso del comando buscar busca cualquier archivo listado después del `-name` opción, luego se ejecuta `awk` ( `/bin/awk`) y ejecuta el mismo script que analizamos en la sección awk para ejecutar un intérprete de shell.

## Usando Exec para iniciar un Shell
```js
find . -exec /bin/sh \; -quit
```
Este uso del comando buscar utiliza la opción de ejecución ( `-exec`) para iniciar el intérprete de shell directamente. Si `find` no se puede encontrar el archivo especificado, no se obtendrá ningún shell.

## VIM
Sí, podemos configurar el idioma del intérprete de shell desde el popular editor de texto basado en línea de comandos. `VIM`. Esta es una situación muy específica en la que nos encontraríamos si tuviéramos que utilizar este método, pero es bueno saberlo por si acaso.

```js
vim -c ':!/bin/sh'
```

```js
vim
:set shell=/bin/sh
:shell
```

## Consideraciones sobre permisos de ejecución

Además de conocer todas las opciones enumeradas anteriormente, debemos tener en cuenta los permisos que tenemos con la cuenta de sesión de shell. Siempre podemos intentar ejecutar este comando para enumerar las propiedades del archivo y los permisos que tiene nuestra cuenta sobre cualquier archivo o binario determinado:
#### Permisos
```shell-session
ls -la <path/to/fileorbinary>
```

También podemos intentar ejecutar este comando para comprobar qué `sudo` permisos que tiene la cuenta a la que llegamos:
#### sudo-l
```js
sudo -l
Matching Defaults entries for apache on ILF-WebSrv:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User apache may run the following commands on ILF-WebSrv:
    (ALL : ALL) NOPASSWD: ALL
```

El comando sudo -l anterior necesitará un shell interactivo estable para ejecutarse. Si no está en un shell completo o sentado en un shell inestable, es posible que no obtenga ningún beneficio de ello. Considerar los permisos no solo nos permitirá ver qué comandos podemos ejecutar, sino que también puede comenzar a darnos una idea de los vectores potenciales que nos permitirán escalar privilegios.