Mucha gente crea sus contraseñas según `simplicity instead of security`. Para eliminar esta debilidad humana que a menudo compromete las medidas de seguridad, se pueden crear políticas de contraseñas en todos los sistemas que determinan cómo debe verse una contraseña. Esto significa que el sistema reconoce si la contraseña contiene letras mayúsculas, caracteres especiales y números. Además, la mayoría de las políticas de contraseñas requieren una longitud mínima de ocho caracteres en una contraseña, incluida al menos una de las especificaciones anteriores.

Desafortunadamente, la tendencia de los usuarios a crear contraseñas débiles también ocurre a pesar de la existencia de políticas de contraseñas. La mayoría de las personas/empleados siguen las mismas reglas al crear contraseñas más complejas. Las contraseñas suelen crearse en estrecha relación con el servicio utilizado. Esto significa que muchos empleados suelen seleccionar contraseñas que pueden tener el nombre de la empresa en las contraseñas. Las preferencias e intereses de una persona también juegan un papel importante. Pueden ser mascotas, amigos, deportes, pasatiempos y muchos otros elementos de la vida. `OSINT`La recopilación de información puede ser muy útil para obtener más información sobre las preferencias de un usuario y puede ayudar a adivinar la contraseña. Puede encontrar más información sobre OSINT en el [módulo OSINT: Corporate Recon](https://academy.hackthebox.com/course/preview/osint-corporate-recon) . Normalmente, los usuarios utilizan las siguientes adiciones a su contraseña para que se ajuste a las políticas de contraseña más comunes:

|**Descripción**|**Sintaxis de contraseña**|
|---|---|
|La primera letra es mayúscula.|`Password`|
|Sumar números.|`Password123`|
|Añadiendo año.|`Password2022`|
|Agregando mes.|`Password02`|
|El último carácter es un signo de exclamación.|`Password2022!`|
|Añadiendo caracteres especiales.|`P@ssw0rd2022!`|
Teniendo en cuenta que muchas personas quieren mantener sus contraseñas lo más simples posible a pesar de las políticas de contraseñas, podemos crear reglas para generar contraseñas débiles. Según las estadísticas proporcionadas por [WPengine](https://wpengine.com/resources/passwords-unmasked-infographic/) , la mayoría de las longitudes de las contraseñas son `not longer` que `ten`caracteres. Entonces, lo que podemos hacer es elegir términos específicos que sean al menos `five`Los caracteres son largos y parecen ser los más familiares para los usuarios, como los nombres de sus mascotas, pasatiempos, preferencias y otros intereses. Si el usuario elige una sola palabra (como el mes actual), agrega la `current year`, seguido de un carácter especial, al final de su contraseña, llegaríamos al `ten-character`requisito de contraseña. Teniendo en cuenta que la mayoría de las empresas requieren cambios regulares de contraseña, un usuario puede modificar su contraseña simplemente cambiando el nombre de un mes o un solo número, etc. Usemos un ejemplo simple para crear una lista de contraseñas con una sola entrada.

Podemos utilizar una herramienta muy poderosa llamada [Hashcat](https://hashcat.net/hashcat/) para combinar listas de nombres y etiquetas potenciales con reglas de mutación específicas para crear listas de palabras personalizadas. Para familiarizarse más con Hashcat y descubrir todo el potencial de esta herramienta, le recomendamos el módulo [Cracking Passwords with Hashcat](https://academy.hackthebox.com/course/preview/cracking-passwords-with-hashcat) . Hashcat utiliza una sintaxis específica para definir caracteres y palabras y cómo se pueden modificar. La lista completa de esta sintaxis se puede encontrar en la [documentación](https://hashcat.net/wiki/doku.php?id=rule_based_attack) oficial de Hashcat. Sin embargo, los que se enumeran a continuación son suficientes para que entendamos cómo Hashcat muta las palabras.

|**Función**|**Descripción**|
|---|---|
|`:`|Hacer nada.|
|`l`|Poner en minúscula todas las letras.|
|`u`|Mayúsculas todas las letras.|
|`c`|Ponga en mayúscula la primera letra y las demás en minúscula.|
|`sXY`|Reemplace todas las instancias de X con Y.|
|`$!`|Añade el carácter de exclamación al final.|
Cada regla está escrita en una nueva línea que determina cómo se debe mutar la palabra. Si escribimos las funciones mostradas arriba en un archivo y consideramos los aspectos mencionados, este archivo puede verse así:

#### Archivo de reglas Hashcat

```js
p314d0@htb[/htb]$ cat custom.rule

:
c
so0
c so0
sa@
c sa@
c sa@ so0
$!
$! c
$! so0
$! sa@
$! c so0
$! c sa@
$! so0 sa@
$! c so0 sa@
```

Hashcat aplicará las reglas de `custom.rule` para cada palabra en `password.list` y almacenar la versión mutada en nuestro `mut_password.list`respectivamente. Por tanto, una palabra dará como resultado quince palabras mutadas en este caso.

```js
hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
cat mut_password.list
```

`Hashcat` y `John`vienen con listas de reglas prediseñadas que podemos usar para generar y descifrar contraseñas. Una de las reglas más utilizadas es `best64.rule`, lo que a menudo puede conducir a buenos resultados. Es importante tener en cuenta que descifrar contraseñas y crear listas de palabras personalizadas es un juego de adivinanzas en la mayoría de los casos. Podemos limitar esto y realizar conjeturas más específicas si tenemos información sobre la política de contraseñas y tomamos en cuenta el nombre de la empresa, la región geográfica, la industria y otros temas/palabras que los usuarios pueden seleccionar para crear sus contraseñas. Las excepciones son, por supuesto, los casos en los que se filtran y encuentran contraseñas.

```js
p314d0@htb[/htb]$ ls /usr/share/hashcat/rules/

best64.rule                  specific.rule
combinator.rule              T0XlC-insert_00-99_1950-2050_toprules_0_F.rule
d3ad0ne.rule                 T0XlC-insert_space_and_special_0_F.rule
dive.rule                    T0XlC-insert_top_100_passwords_1_G.rule
generated2.rule              T0XlC.rule
generated.rule               T0XlCv1.rule
hybrid                       toggles1.rule
Incisive-leetspeak.rule      toggles2.rule
InsidePro-HashManager.rule   toggles3.rule
InsidePro-PasswordsPro.rule  toggles4.rule
leetspeak.rule               toggles5.rule
oscommerce.rule              unix-ninja-leetspeak.rule
rockyou-30000.rule
```

Ahora podemos utilizar otra herramienta llamada [CeWL](https://github.com/digininja/CeWL) para escanear palabras potenciales del sitio web de la empresa y guardarlas en una lista separada. Luego podemos combinar esta lista con las reglas deseadas y crear una lista de contraseñas personalizada que tenga una mayor probabilidad de adivinar la contraseña correcta. Especificamos algunos parámetros, como la profundidad de la araña ( `-d`), la longitud mínima de la palabra ( `-m`), el almacenamiento de las palabras encontradas en minúsculas ( `--lowercase`), así como el archivo donde queremos almacenar los resultados ( `-w`).



```js
p314d0@htb[/htb]$ cewl https://www.inlanefreight.com -d 4 -m 6 --lowercase -w inlane.wordlist
p314d0@htb[/htb]$ wc -l inlane.wordlist
```

hydra -l sam -P sam_password.list ftp://10.129.32.75 -t48

[21][ftp] host: 10.129.32.75   login: sam   password: B@tm@n2022!
sam : B@tm@n2022!