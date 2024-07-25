Para comenzar a interactuar con Metasploit Framework, debemos escribir `msfconsole`en el terminal de nuestra elección.

Alternativamente, podemos utilizar el `-q` opción, que no muestra el banner.
```js
p314d0@htb[/htb]$ msfconsole -q

msf6 > 
```

Para ver mejor todos los comandos disponibles, podemos escribir el `help`dominio

#### Instalación de MSF
```js
p314d0@htb[/htb]$ sudo apt update && sudo apt install metasploit-framework

<SNIP>

(Reading database ... 414458 files and directories currently installed.)
Preparing to unpack .../metasploit-framework_6.0.2-0parrot1_amd64.deb ...
Unpacking metasploit-framework (6.0.2-0parrot1) over (5.0.88-0kali1) ...
Setting up metasploit-framework (6.0.2-0parrot1) ...
Processing triggers for man-db (2.9.1-1) ...
Scanning application launchers
Removing duplicate launchers from Debian
Launchers are updated
```

## Estructura de participación de MSF
La estructura de participación de MSF se puede dividir en cinco categorías principales.
- Enumeración
- Preparación
- Explotación
- Escalada de privilegios
- Post-explotación
Esta división nos facilita encontrar y seleccionar las características apropiadas de MSF de una manera más estructurada y trabajar con ellas en consecuencia. Cada una de estas categorías tiene diferentes subcategorías que están destinadas a propósitos específicos. Estos incluyen, por ejemplo, validación de servicios e investigación de vulnerabilidades.

![](https://academy.hackthebox.com/storage/modules/39/S04_SS03.png)

