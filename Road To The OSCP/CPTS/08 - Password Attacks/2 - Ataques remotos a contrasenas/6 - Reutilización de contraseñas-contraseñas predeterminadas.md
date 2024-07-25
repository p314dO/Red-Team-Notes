
---

Es común que tanto los usuarios como los administradores dejen los valores predeterminados. Los administradores deben realizar un seguimiento de toda la tecnología, la infraestructura y las aplicaciones junto con los datos a los que se accede. En este caso, `the same password`se utiliza a menudo con fines de configuración, y luego se olvida la contraseña para cambiarla para una interfaz u otra. Además, muchas aplicaciones que funcionan con mecanismos de autenticación, básicamente casi todas, suelen venir con `default credentials`Después de la instalación. Es posible que se olvide cambiar estas credenciales predeterminadas después de la configuración, especialmente cuando se trata de aplicaciones internas donde los administradores asumen que nadie más las encontrará y ni siquiera intentan usarlas.

Además, las contraseñas fáciles de recordar que se pueden escribir rápidamente en lugar de escribir contraseñas de 15 caracteres a menudo se usan repetidamente porque [el inicio de sesión único](https://en.wikipedia.org/wiki/Single_sign-on) ( `SSO`) no siempre está disponible de inmediato durante la instalación inicial y la configuración en las redes internas requiere cambios significativos. Al configurar redes, a veces trabajamos con infraestructuras enormes (dependiendo del tamaño de la empresa) que pueden tener cientos de interfaces. A menudo se pasa por alto un dispositivo de red, como un enrutador, una impresora o un firewall, y el `default credentials` se utilizan, o lo mismo `password is reused`.

## Credential Stuffing
Existen varias bases de datos que mantienen una lista actualizada de credenciales predeterminadas conocidas. Uno de ellos es [DefaultCreds-Cheat-Sheet](https://github.com/ihebski/DefaultCreds-cheat-sheet) . Aquí hay un pequeño extracto de la tabla completa de esta hoja de trucos:

|**Product/Vendor**|**Username**|**Password**|
|---|---|---|
|Zyxel (ssh)|zyfwp|PrOw!aN_fXp|
|APC UPS (web)|apc|apc|
|Weblogic (web)|system|manager|
|Weblogic (web)|system|manager|
|Weblogic (web)|weblogic|weblogic1|
|Weblogic (web)|WEBLOGIC|WEBLOGIC|
|Weblogic (web)|PUBLIC|PUBLIC|
|Weblogic (web)|EXAMPLES|EXAMPLES|
|Weblogic (web)|weblogic|weblogic|
|Weblogic (web)|system|password|
|Weblogic (web)|weblogic|welcome(1)|
|Weblogic (web)|system|welcome(1)|
|Weblogic (web)|operator|weblogic|
|Weblogic (web)|operator|password|
|Weblogic (web)|system|Passw0rd|
|Weblogic (web)|monitor|password|
|Kanboard (web)|admin|admin|
|Vectr (web)|admin|11_ThisIsTheFirstPassword_11|
|Caldera (web)|admin|admin|
|Dlink (web)|admin|admin|
|Dlink (web)|1234|1234|
|Dlink (web)|root|12345|
|Dlink (web)|root|root|
|JioFiber|admin|jiocentrum|
|GigaFiber|admin|jiocentrum|
|Kali linux (OS)|kali|kali|
|F5|admin|admin|
|F5|root|default|
|F5|support||
|...|...||
Las credenciales predeterminadas también se pueden encontrar en la documentación del producto, ya que contienen los pasos necesarios para configurar el servicio correctamente. Algunos dispositivos/aplicaciones requieren que el usuario configure una contraseña durante la instalación, pero otros usan una contraseña débil predeterminada. Atacar esos servicios con las credenciales predeterminadas u obtenidas se llama [Credential Stuffing](https://owasp.org/www-community/attacks/Credential_stuffing) . Esta es una variante simplificada de la fuerza bruta porque sólo se utilizan nombres de usuario compuestos y las contraseñas asociadas.

Podemos imaginar que hemos encontrado algunas aplicaciones utilizadas en la red por nuestros clientes. Después de buscar en Internet las credenciales predeterminadas, podemos crear una nueva lista que separe estas credenciales compuestas con dos puntos ( `username:password`). Además, podremos seleccionar las contraseñas y mutarlas mediante nuestro `rules` para aumentar la probabilidad de aciertos.

#### Credential Stuffing - Hydra Syntax
```js
hydra -C <user_pass.list> <protocol>://<IP>
```

```js
hydra -C user_pass.list ssh://10.129.42.197
```

Además de las credenciales predeterminadas para aplicaciones, algunas listas las ofrecen para enrutadores. Una de estas listas se puede encontrar [aquí](https://www.softwaretestinghelp.com/default-router-username-and-password-list/) . Es mucho menos probable que las credenciales predeterminadas de los enrutadores no se modifiquen. Dado que estas son las interfaces centrales de las redes, los administradores suelen prestar mucha más atención a reforzarlas. Sin embargo, todavía es posible que se pase por alto un enrutador o que actualmente solo se utilice en la red interna con fines de prueba, lo que luego podemos aprovechar para futuros ataques.

|**Router Brand**|**Default IP Address**|**Default Username**|**Default Password**|
|---|---|---|---|
|3Com|http://192.168.1.1|admin|Admin|
|Belkin|http://192.168.2.1|admin|admin|
|BenQ|http://192.168.1.1|admin|Admin|
|D-Link|http://192.168.0.1|admin|Admin|
|Digicom|http://192.168.1.254|admin|Michelangelo|
|Linksys|http://192.168.1.1|admin|Admin|
|Netgear|http://192.168.0.1|admin|password|

-----
Utilice las credenciales de usuario que encontramos en la sección anterior y descubra las credenciales para MySQL. Envíe las credenciales como respuesta. (Formato: nombre de usuario:contraseña)

```js
┌──(pelado㉿kali)-[~/EthicalHacking/HTB/Academy/AttackPasswords]
└─$ nmap -sCV -p- --min-rate=1000 -Pn -n 10.129.32.75  
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-27 13:46 EDT
Nmap scan report for 10.129.32.75
Host is up (0.15s latency).
Not shown: 65529 closed tcp ports (conn-refused)
PORT      STATE    SERVICE     VERSION
21/tcp    open     ftp         vsftpd 3.0.3
22/tcp    open     ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 3f:4c:8f:10:f1:ae:be:cd:31:24:7c:a1:4e:ab:84:6d (RSA)
|   256 7b:30:37:67:50:b9:ad:91:c0:8f:f7:02:78:3b:7c:02 (ECDSA)
|_  256 88:9e:0e:07:fe:ca:d0:5c:60:ab:cf:10:99:cd:6c:a7 (ED25519)
139/tcp   open     netbios-ssn Samba smbd 4.6.2
445/tcp   open     netbios-ssn Samba smbd 4.6.2
58798/tcp filtered unknown
59350/tcp filtered unknown
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_nbstat: NetBIOS name: NIX01, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_clock-skew: -1s
| smb2-time: 
|   date: 2024-06-27T17:47:46
|_  start_date: N/A

```

https://raw.githubusercontent.com/ihebski/DefaultCreds-cheat-sheet/main/DefaultCreds-Cheat-Sheet.csv

![[Pasted image 20240627141733.png]]