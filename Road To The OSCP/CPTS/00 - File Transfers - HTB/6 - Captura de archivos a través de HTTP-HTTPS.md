## HTTP/S
La transferencia web es la forma más común en que la mayoría de las personas transfieren archivos porque `HTTP`/ `HTTPS`son los protocolos más comunes permitidos a través de firewalls. Otro inmenso beneficio es que, en muchos casos, el archivo se cifrará en tránsito. No hay nada peor que estar en una prueba de penetración, y el IDS de la red de un cliente detecta un archivo confidencial que se transfiere en texto plano y le pregunta por qué enviamos una contraseña a nuestro servidor en la nube sin usar cifrado. Ya hemos hablado del uso del [módulo uploadserver](https://github.com/Densaugeo/uploadserver) de Python3 para configurar un servidor web con capacidades de carga, pero también podemos usar Apache o Nginx. Esta sección cubrirá la creación de un servidor web seguro para operaciones de carga de archivos.

## Nginx: habilitación de PUT
Una buena alternativa para transferir archivos a Apache es Nginx porque la configuración es menos complicada, y el sistema de módulos no conduce a problemas de seguridad como Apache. Cuando se permiten subidas HTTP, es crítico estar 100% seguro de que los usuarios no pueden subir web shells y ejecutarlos. Apache hace que sea fácil dispararnos en el pie con esto, ya que al módulo PHP le encanta ejecutar cualquier cosa que termine en PHP. Configurar Nginx para usar PHP no es tan sencillo.

#### Create a Directory to Handle Uploaded Files
```js
sudo mkdir -p /var/www/uploads/SecretUploadDirectory
```

#### Change the Owner to www-data
```js
sudo chown -R www-data:www-data /var/www/uploads/SecretUploadDirectory
```

#### Create Nginx Configuration File
Cree el archivo de configuración de Nginx creando el archivo `/etc/nginx/sites-available/upload.conf` con el contenido:
```js
server {
    listen 9001;
    
    location /SecretUploadDirectory/ {
        root    /var/www/uploads;
        dav_methods PUT;
    }
}
```

#### Vincular simbólicamente nuestro sitio al directorio habilitado para sitios
```js
sudo ln -s /etc/nginx/sites-available/upload.conf /etc/nginx/sites-enabled/
```

#### Iniciar Nginx
```js
sudo systemctl restart nginx.service
```
Si recibimos algún mensaje de error, verifique `/var/log/nginx/error.log`.

#### Verificación de errores
```js
p314d0@htb[/htb]$ tail -2 /var/log/nginx/error.log

2020/11/17 16:11:56 [emerg] 5679#5679: bind() to 0.0.0.0:`80` failed (98: A`ddress already in use`)
2020/11/17 16:11:56 [emerg] 5679#5679: still could not bind()
```

```js
p314d0@htb[/htb]$ ss -lnpt | grep 80

LISTEN 0      100          0.0.0.0:80        0.0.0.0:*    users:(("python",pid=`2811`,fd=3),("python",pid=2070,fd=3),("python",pid=1968,fd=3),("python",pid=1856,fd=3))
```

```js
p314d0@htb[/htb]$ ps -ef | grep 2811

user65      2811    1856  0 16:05 ?        00:00:04 `python -m websockify 80 localhost:5901 -D`
root        6720    2226  0 16:14 pts/0    00:00:00 grep --color=auto 2811
```
Vemos que ya hay un módulo escuchando en el puerto 80. Para solucionar esto, podemos eliminar la configuración predeterminada de Nginx, que se vincula en el puerto 80.

#### Eliminar la configuración predeterminada de Nginx
```js
p314d0@htb[/htb]$ sudo rm /etc/nginx/sites-enabled/default
```

Ahora podemos probar la carga usando `cURL` enviar un `PUT`pedido. En el siguiente ejemplo, cargaremos el `/etc/passwd` archivo al servidor y llámelo usuarios.txt
#### Cargar archivo usando cURL
```js
curl -T /etc/passwd http://localhost:9001/SecretUploadDirectory/users.txt
```

```js
sudo tail -1 /var/www/uploads/SecretUploadDirectory/users.txt 
```

Una vez que tengamos esto funcionando, una buena prueba es asegurarse de que la lista del directorio no esté habilitada navegando a `http://localhost/SecretUploadDirectory`. Por defecto, con `Apache`, si llegamos a un directorio sin un archivo de índice (index.html), enumerará todos los archivos. Esto es malo para nuestro caso de uso de eliminación de archivos porque la mayoría de los archivos son confidenciales por naturaleza y queremos hacer todo lo posible para ocultarlos. Gracias a `Nginx` Al ser mínimas, funciones como esa no están habilitadas de forma predeterminada.