#### Sintaxis
```shell-session
<No.> <type>/<os>/<service>/<name>
```

#### Example
```js
794   exploit/windows/ftp/scriptftp_list
```


| **Tipo**    | **Descripción**                                                                                                      |
| ----------- | -------------------------------------------------------------------------------------------------------------------- |
| `Auxiliary` | Capacidades de escaneo, fuzzing, sniffing y administración. Ofrezca asistencia y funcionalidad adicionales.          |
| `Encoders`  | Asegúrese de que las cargas útiles estén intactas hasta su destino.                                                  |
| `Exploits`  | Definidos como módulos que explotan una vulnerabilidad que permitirá la entrega de carga útil.                       |
| `NOPs`      | (Sin código de operación) Mantenga los tamaños de carga útiles consistentes en todos los intentos de explotación.    |
| `Payloads`  | El código se ejecuta de forma remota y vuelve a llamar a la máquina atacante para establecer una conexión (o shell). |
| `Plugins`   | Se pueden integrar guiones adicionales dentro de una evaluación con `msfconsole` y convivir.                         |
| `Post`      | Amplia gama de módulos para recopilar información, profundizar, etc.                                                 |
| **Tipo**    | **Descripción**                                                                                                      |
| `Auxiliary` | Capacidades de escaneo, fuzzing, sniffing y administración. Ofrezca asistencia y funcionalidad adicionales.          |
| `Exploits`  | Definidos como módulos que explotan una vulnerabilidad que permitirá la entrega de carga útil.                       |
| `Post`      | Amplia gama de módulos para recopilar información, profundizar, etc.                                                 |

## Buscando módulos
#### MSF - Search Function
```js
msf6 > help search
```

Examples:
  search cve:2009 type:exploit
  search cve:2009 type:exploit platform:-linux
  search cve:2009 -s name
  search type:exploit -s type -r

Por ejemplo, podemos intentar encontrar el `EternalRomance`exploit para sistemas operativos Windows más antiguos
#### MSF - En busca del romance eterno

```js
msf6 > search eternalromance
```

También podemos hacer nuestra búsqueda un poco más aproximada y reducirla a una categoría de servicios. Por ejemplo, para el CVE, podríamos especificar el año ( `cve:<year>`), la plataforma Windows ( `platform:<os>`), el tipo de módulo que queremos encontrar ( `type:<auxiliary/exploit/post>`), el rango de confiabilidad ( `rank:<rank>`) y el nombre de la búsqueda ( `<pattern>`). Esto reduciría nuestros resultados a solo aquellos que coincidan con todo lo anterior.

#### MSF - Búsqueda específica
```js
msf6 > search type:exploit platform:windows cve:2021 rank:excellent microsoft
```

## Selección de módulo
Para seleccionar nuestro primer módulo, primero debemos encontrar uno. Supongamos que tenemos un objetivo ejecutando una versión de SMB vulnerable a los exploits EternalRomance (MS17_010). Descubrimos que el puerto 445 del servidor SMB está abierto al escanear el objetivo.

```js
┌──(pelado㉿kali)-[~/OSCP/HTB/Academy/Metasploit]
└─$ nmap -sCV --min-rate=1000 -Pn -n 10.129.2.141 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-24 16:07 EDT
Nmap scan report for 10.129.2.141
Host is up (0.17s latency).
Not shown: 984 closed tcp ports (conn-refused)
PORT      STATE    SERVICE         VERSION
80/tcp    open     http            Microsoft IIS httpd 10.0
|_http-title: 10.129.2.141 - /
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
135/tcp   open     msrpc           Microsoft Windows RPC
139/tcp   open     netbios-ssn     Microsoft Windows netbios-ssn
445/tcp   open     microsoft-ds    Windows Server 2016 Standard 14393 microsoft-ds
1900/tcp  filtered upnp
2121/tcp  filtered ccproxy-ftp
3011/tcp  filtered trusted-web
3367/tcp  filtered satvid-datalnk
5730/tcp  filtered unieng
6502/tcp  filtered netop-rc
7000/tcp  filtered afs3-fileserver
8010/tcp  filtered xmpp
8081/tcp  filtered blackice-icecap
20031/tcp filtered unknown
30000/tcp filtered ndmps
52822/tcp filtered unknown
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: MSF1-WIN01
|   NetBIOS computer name: MSF1-WIN01\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2024-06-24T13:07:21-07:00
|_clock-skew: mean: 2h19m55s, deviation: 4h02m30s, median: -5s
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2024-06-24T20:07:24
|_  start_date: 2024-06-24T19:51:57

```

Además, existe la opción `setg`, que especifica las opciones seleccionadas por nosotros como permanentes hasta que se reinicie el programa.

```shell-session
msf6 exploit(windows/smb/ms17_010_psexec) > setg RHOSTS 10.10.10.40
```

Una vez que todo esté configurado y listo para funcionar, podemos proceder a lanzar el ataque. Tenga en cuenta que la carga útil no se configuró aquí, ya que la predeterminada es suficiente para esta demostración.

```js
msf6 exploit(windows/smb/ms17_010_eternalblue) 
```

Use the Metasploit-Framework to exploit the target with EternalRomance. Find the flag.txt file on Administrator's desktop and submit the contents as the answer.

msf6 exploit(windows/smb/ms17_010_psexec)
