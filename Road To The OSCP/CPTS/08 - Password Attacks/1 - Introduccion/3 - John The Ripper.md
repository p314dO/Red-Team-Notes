Es una herramienta de pentesting esencial que se utiliza para comprobar la seguridad de las contraseñas y descifrar contraseñas cifradas (o hash) mediante ataques de fuerza bruta o de diccionario. Es un software de código abierto desarrollado inicialmente para sistemas basados ​​en UNIX y lanzado por primera vez en 1996. Se ha convertido en un elemento básico para los profesionales de la seguridad debido a sus diversas capacidades. La variante "Jumbo" se recomienda para quienes trabajan en el campo de la seguridad, ya que tiene optimizaciones de rendimiento y características adicionales como listas de palabras multilingües y soporte para arquitecturas de 64 bits. Esta versión es más efectiva para descifrar contraseñas con mayor precisión y velocidad.

|**Tecnología de cifrado**|**Descripción**|
|---|---|
|`UNIX crypt(3)`|Crypt(3) es un sistema de cifrado UNIX tradicional con una clave de 56 bits.|
|`Traditional DES-based`|El cifrado basado en DES utiliza el algoritmo del estándar de cifrado de datos para cifrar los datos.|
|`bigcrypt`|Bigcrypt es una extensión del cifrado tradicional basado en DES. Utiliza una clave de 128 bits.|
|`BSDI extended DES-based`|El cifrado extendido basado en DES BSDI es una extensión del cifrado tradicional basado en DES y utiliza una clave de 168 bits.|
|`FreeBSD MD5-based` (Linux y Cisco)|El cifrado basado en FreeBSD MD5 utiliza el algoritmo MD5 para cifrar datos con una clave de 128 bits.|
|`OpenBSD Blowfish-based`|El cifrado basado en OpenBSD Blowfish utiliza el algoritmo Blowfish para cifrar datos con una clave de 448 bits.|
|`Kerberos/AFS`|Kerberos y AFS son sistemas de autenticación que utilizan cifrado para garantizar una comunicación segura entre entidades.|
|`Windows LM`|El cifrado de Windows LM utiliza el algoritmo estándar de cifrado de datos para cifrar datos con una clave de 56 bits.|
|`DES-based tripcodes`|Los códigos de viaje basados ​​en DES se utilizan para autenticar a los usuarios según el algoritmo del estándar de cifrado de datos.|
|`SHA-crypt hashes`|Los hash SHA-crypt se utilizan para cifrar datos con una clave de 256 bits y están disponibles en versiones más recientes de Fedora y Ubuntu.|
|`SHA-crypt` y `SUNMD5 hashes` (Solaris)|Los hashes SHA-crypt y SUNMD5 utilizan los algoritmos SHA-crypt y MD5 para cifrar datos con una clave de 256 bits y están disponibles en Solaris.|
|`...`|y muchos más.|

---
## Métodos de ataque
#### Ataques de diccionario
Los ataques de diccionario implican el uso de una lista pregenerada de palabras y frases (conocida como diccionario) para intentar descifrar una contraseña. Esta lista de palabras y frases suele obtenerse de diversas fuentes, como diccionarios disponibles públicamente, contraseñas filtradas o incluso adquiridas en empresas especializadas. Luego, el diccionario se usa para generar una serie de cadenas que luego se usan para comparar con las contraseñas hash. Si se encuentra una coincidencia, la contraseña se descifra, proporcionando al atacante acceso al sistema y a los datos almacenados en él. Este tipo de ataque es muy eficaz. Por lo tanto, es esencial tomar las medidas necesarias para garantizar que las contraseñas se mantengan seguras, como usar contraseñas complejas y únicas, cambiarlas periódicamente y utilizar autenticación de dos factores.

#### Ataques de fuerza bruta
Los ataques de fuerza bruta implican intentar todas las combinaciones imaginables de caracteres que podrían formar una contraseña. Este es un proceso extremadamente lento y, por lo general, solo se recomienda utilizar este método si no hay otras alternativas. También es importante tener en cuenta que cuanto más larga y compleja sea la contraseña, más difícil será descifrarla y más tiempo llevará agotar todas las combinaciones. Por este motivo, es muy recomendable que las contraseñas tengan al menos 8 caracteres de longitud, con una combinación de letras, números y símbolos.

#### Rainbow Table Attacks
Los ataques de tabla Rainbow implican el uso de una tabla precalculada de hashes y sus correspondientes contraseñas de texto sin formato, que es un método mucho más rápido que un ataque de fuerza bruta. Sin embargo, este método está limitado por el tamaño de la tabla del arco iris: cuanto más grande sea la tabla, más contraseñas y hashes podrá almacenar. Además, debido a la naturaleza del ataque, es imposible utilizar tablas de arcoíris para determinar el texto sin formato de los hashes que aún no están incluidos en la tabla. Como resultado, los ataques a la tabla arcoíris solo son efectivos contra los hashes que ya están presentes en la tabla, por lo que cuanto más grande sea la tabla, más exitoso será el ataque.

## Modos de craqueo
`Single Crack Mode`es uno de los modos John más comunes que se utilizan al intentar descifrar contraseñas utilizando una lista de contraseñas única. Es un ataque de fuerza bruta, lo que significa que se prueban todas las contraseñas de la lista, una por una, hasta encontrar la correcta. Este método es la forma más básica y sencilla de descifrar contraseñas y, por lo tanto, es una opción popular para quienes desean obtener acceso a un sistema seguro. Sin embargo, está lejos de ser el método más eficaz, ya que puede llevar un tiempo indefinido descifrar una contraseña, dependiendo de la longitud y complejidad de la contraseña en cuestión. La sintaxis básica del comando es:

```rb
john --format=<hash_type> <hash or hash_file>
```

Por ejemplo, si tenemos un archivo llamado `hashes_to_crack.txt` eso contiene `SHA-256` hashes, el comando para descifrarlos sería:
```js
john --format=sha256 hashes_to_crack.txt
```

- `john` es el comando para ejecutar el programa John the Ripper
- `--format=sha256` especifica que el formato hash es SHA-256
- `hashes.txt` es el nombre del archivo que contiene los hashes que se van a descifrar

Cuando ejecutamos el comando, John leerá los hashes del archivo especificado y luego intentará descifrarlos comparándolos con las palabras en su lista de palabras incorporada y cualquier lista de palabras adicional especificada con el `--wordlist`opción. Además, utilizará cualquier regla establecida con el `--rules` opción (si se dan reglas) para generar más contraseñas candidatas.

John enviará las contraseñas descifradas a la consola y al archivo "john.pot" ( `~/.john/john.pot`) al directorio de inicio del usuario actual. Además, continuará descifrando los hashes restantes en segundo plano y podemos verificar el progreso ejecutando el `john --show`dominio. Para maximizar las posibilidades de éxito, es importante asegurarse de que las listas de palabras y las reglas utilizadas sean completas y estén actualizadas.

#### Cracking with John
|**Hash Format**|**Example Command**|**Description**|
|---|---|---|
|afs|`john --format=afs hashes_to_crack.txt`|AFS (Andrew File System) password hashes|
|bfegg|`john --format=bfegg hashes_to_crack.txt`|bfegg hashes used in Eggdrop IRC bots|
|bf|`john --format=bf hashes_to_crack.txt`|Blowfish-based crypt(3) hashes|
|bsdi|`john --format=bsdi hashes_to_crack.txt`|BSDi crypt(3) hashes|
|crypt(3)|`john --format=crypt hashes_to_crack.txt`|Traditional Unix crypt(3) hashes|
|des|`john --format=des hashes_to_crack.txt`|Traditional DES-based crypt(3) hashes|
|dmd5|`john --format=dmd5 hashes_to_crack.txt`|DMD5 (Dragonfly BSD MD5) password hashes|
|dominosec|`john --format=dominosec hashes_to_crack.txt`|IBM Lotus Domino 6/7 password hashes|
|EPiServer SID hashes|`john --format=episerver hashes_to_crack.txt`|EPiServer SID (Security Identifier) password hashes|
|hdaa|`john --format=hdaa hashes_to_crack.txt`|hdaa password hashes used in Openwall GNU/Linux|
|hmac-md5|`john --format=hmac-md5 hashes_to_crack.txt`|hmac-md5 password hashes|
|hmailserver|`john --format=hmailserver hashes_to_crack.txt`|hmailserver password hashes|
|ipb2|`john --format=ipb2 hashes_to_crack.txt`|Invision Power Board 2 password hashes|
|krb4|`john --format=krb4 hashes_to_crack.txt`|Kerberos 4 password hashes|
|krb5|`john --format=krb5 hashes_to_crack.txt`|Kerberos 5 password hashes|
|LM|`john --format=LM hashes_to_crack.txt`|LM (Lan Manager) password hashes|
|lotus5|`john --format=lotus5 hashes_to_crack.txt`|Lotus Notes/Domino 5 password hashes|
|mscash|`john --format=mscash hashes_to_crack.txt`|MS Cache password hashes|
|mscash2|`john --format=mscash2 hashes_to_crack.txt`|MS Cache v2 password hashes|
|mschapv2|`john --format=mschapv2 hashes_to_crack.txt`|MS CHAP v2 password hashes|
|mskrb5|`john --format=mskrb5 hashes_to_crack.txt`|MS Kerberos 5 password hashes|
|mssql05|`john --format=mssql05 hashes_to_crack.txt`|MS SQL 2005 password hashes|
|mssql|`john --format=mssql hashes_to_crack.txt`|MS SQL password hashes|
|mysql-fast|`john --format=mysql-fast hashes_to_crack.txt`|MySQL fast password hashes|
|mysql|`john --format=mysql hashes_to_crack.txt`|MySQL password hashes|
|mysql-sha1|`john --format=mysql-sha1 hashes_to_crack.txt`|MySQL SHA1 password hashes|
|NETLM|`john --format=netlm hashes_to_crack.txt`|NETLM (NT LAN Manager) password hashes|
|NETLMv2|`john --format=netlmv2 hashes_to_crack.txt`|NETLMv2 (NT LAN Manager version 2) password hashes|
|NETNTLM|`john --format=netntlm hashes_to_crack.txt`|NETNTLM (NT LAN Manager) password hashes|
|NETNTLMv2|`john --format=netntlmv2 hashes_to_crack.txt`|NETNTLMv2 (NT LAN Manager version 2) password hashes|
|NEThalfLM|`john --format=nethalflm hashes_to_crack.txt`|NEThalfLM (NT LAN Manager) password hashes|
|md5ns|`john --format=md5ns hashes_to_crack.txt`|md5ns (MD5 namespace) password hashes|
|nsldap|`john --format=nsldap hashes_to_crack.txt`|nsldap (OpenLDAP SHA) password hashes|
|ssha|`john --format=ssha hashes_to_crack.txt`|ssha (Salted SHA) password hashes|
|NT|`john --format=nt hashes_to_crack.txt`|NT (Windows NT) password hashes|
|openssha|`john --format=openssha hashes_to_crack.txt`|OPENSSH private key password hashes|
|oracle11|`john --format=oracle11 hashes_to_crack.txt`|Oracle 11 password hashes|
|oracle|`john --format=oracle hashes_to_crack.txt`|Oracle password hashes|
|pdf|`john --format=pdf hashes_to_crack.txt`|PDF (Portable Document Format) password hashes|
|phpass-md5|`john --format=phpass-md5 hashes_to_crack.txt`|PHPass-MD5 (Portable PHP password hashing framework) password hashes|
|phps|`john --format=phps hashes_to_crack.txt`|PHPS password hashes|
|pix-md5|`john --format=pix-md5 hashes_to_crack.txt`|Cisco PIX MD5 password hashes|
|po|`john --format=po hashes_to_crack.txt`|Po (Sybase SQL Anywhere) password hashes|
|rar|`john --format=rar hashes_to_crack.txt`|RAR (WinRAR) password hashes|
|raw-md4|`john --format=raw-md4 hashes_to_crack.txt`|Raw MD4 password hashes|
|raw-md5|`john --format=raw-md5 hashes_to_crack.txt`|Raw MD5 password hashes|
|raw-md5-unicode|`john --format=raw-md5-unicode hashes_to_crack.txt`|Raw MD5 Unicode password hashes|
|raw-sha1|`john --format=raw-sha1 hashes_to_crack.txt`|Raw SHA1 password hashes|
|raw-sha224|`john --format=raw-sha224 hashes_to_crack.txt`|Raw SHA224 password hashes|
|raw-sha256|`john --format=raw-sha256 hashes_to_crack.txt`|Raw SHA256 password hashes|
|raw-sha384|`john --format=raw-sha384 hashes_to_crack.txt`|Raw SHA384 password hashes|
|raw-sha512|`john --format=raw-sha512 hashes_to_crack.txt`|Raw SHA512 password hashes|
|salted-sha|`john --format=salted-sha hashes_to_crack.txt`|Salted SHA password hashes|
|sapb|`john --format=sapb hashes_to_crack.txt`|SAP CODVN B (BCODE) password hashes|
|sapg|`john --format=sapg hashes_to_crack.txt`|SAP CODVN G (PASSCODE) password hashes|
|sha1-gen|`john --format=sha1-gen hashes_to_crack.txt`|Generic SHA1 password hashes|
|skey|`john --format=skey hashes_to_crack.txt`|S/Key (One-time password) hashes|
|ssh|`john --format=ssh hashes_to_crack.txt`|SSH (Secure Shell) password hashes|
|sybasease|`john --format=sybasease hashes_to_crack.txt`|Sybase ASE password hashes|
|xsha|`john --format=xsha hashes_to_crack.txt`|xsha (Extended SHA) password hashes|
|zip|`john --format=zip hashes_to_crack.txt`|ZIP (WinZip) password hashes|
#### Modo de lista de palabras
`Wordlist Mode`se utiliza para descifrar contraseñas utilizando múltiples listas de palabras. Es un ataque de diccionario, lo que significa que probará todas las palabras de las listas una por una hasta encontrar la correcta. Generalmente se usa para descifrar múltiples hashes de contraseñas usando una lista de palabras o una combinación de listas de palabras. Es más efectivo que el modo Single Crack porque utiliza más palabras pero sigue siendo relativamente básico. La sintaxis básica del comando es:

```js
john --wordlist=<wordlist_file> --rules <hash_file>
```

Primero, especificamos el archivo o archivos de lista de palabras que se utilizarán para descifrar los hash de contraseña. La lista de palabras puede estar en formato de texto plano, con una palabra por línea. Se pueden especificar varias listas de palabras separándolas con una coma. Luego podemos especificar un conjunto de reglas o aplicar las reglas de manipulación integradas a las palabras de la lista de palabras. Estas reglas generan contraseñas candidatas mediante transformaciones como agregar números, poner letras en mayúscula y agregar caracteres especiales.

#### Modo incremental
`Incremental Mode`es un modo John avanzado que se utiliza para descifrar contraseñas utilizando un conjunto de caracteres. Es un ataque híbrido, lo que significa que intentará hacer coincidir la contraseña probando todas las combinaciones posibles de caracteres del conjunto de caracteres. Este modo es el más eficaz y, al mismo tiempo, el que consume más tiempo de todos los modos John. Este modo funciona mejor cuando sabemos cuál podría ser la contraseña, ya que probará todas las combinaciones posibles en secuencia, comenzando por la más corta. Esto lo hace mucho más rápido que el ataque de fuerza bruta, donde todas las combinaciones se prueban al azar. Además, el modo incremental también se puede utilizar para descifrar contraseñas débiles, que pueden resultar difíciles de descifrar con los modos estándar de John. La principal diferencia entre el modo incremental y el modo de lista de palabras es la fuente de las adivinanzas de contraseña. El modo incremental genera conjeturas sobre la marcha, mientras que el modo de lista de palabras utiliza una lista predefinida de palabras. Al mismo tiempo, el modo de descifrado único se utiliza para comparar una única contraseña con un hash.

```js
john --incremental <hash_file>
```

Usando este comando leeremos los hashes en el archivo hash especificado y luego generaremos todas las combinaciones posibles de caracteres, comenzando con un solo carácter e incrementando con cada iteración. Es importante tener en cuenta que este modo es `highly resource intensive`y puede tardar mucho en completarse, dependiendo de la complejidad de las contraseñas, la configuración de la máquina y la cantidad de caracteres establecidos. Además, es importante tener en cuenta que el juego de caracteres predeterminado está limitado a `a-zA-Z0-9`. Por lo tanto, si intentamos descifrar contraseñas complejas con caracteres especiales, necesitamos utilizar un juego de caracteres personalizado.

## Descifrar archivos
También es posible descifrar incluso archivos cifrados o protegidos con contraseña con John. Usamos herramientas adicionales que procesan los archivos proporcionados y producen hashes con los que John puede trabajar. Detecta automáticamente los formatos e intenta descifrarlos. La sintaxis para esto puede verse así:
#### Descifrando archivos con John
```js
cry0l1t3@htb:~$ <tool> <file_to_crack> > file.hash
cry0l1t3@htb:~$ pdf2john server_doc.pdf > server_doc.hash
cry0l1t3@htb:~$ john server_doc.hash
                # OR
cry0l1t3@htb:~$ john --wordlist=<wordlist.txt> server_doc.hash 
```

Además, podemos usar diferentes modos para esto con nuestras listas de palabras y reglas personales. Hemos creado una lista que incluye muchas, pero no todas, las herramientas que se pueden utilizar para John:

|**Herramienta**|**Descripción**|
|---|---|
|`pdf2john`|Convierte documentos PDF para John|
|`ssh2john`|Convierte claves privadas SSH para John|
|`mscash2john`|Convierte hashes de MS Cash para John|
|`keychain2john`|Convierte archivos de llavero de OS X para John|
|`rar2john`|Convierte archivos RAR para John|
|`pfx2john`|Convierte archivos PKCS#12 para John|
|`truecrypt_volume2john`|Convierte volúmenes TrueCrypt para John|
|`keepass2john`|Convierte bases de datos KeePass para John|
|`vncpcap2john`|Convierte archivos VNC PCAP para John|
|`putty2john`|Convierte claves privadas PuTTY para John|
|`zip2john`|Convierte archivos ZIP para John|
|`hccap2john`|Convierte capturas de protocolo de enlace WPA/WPA2 para John|
|`office2john`|Convierte documentos de MS Office para John|
|`wpa2john`|Convierte protocolos de enlace WPA/WPA2 para John|
