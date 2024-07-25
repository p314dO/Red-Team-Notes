MSFconsole puede gestionar varios módulos al mismo tiempo. Esta es una de las muchas razones por las que proporciona tanta flexibilidad al usuario. Esto se hace con el uso de `Sessions`, que crea interfaces de control dedicadas para todos los módulos implementados.

## Usando sesiones
Mientras ejecutamos cualquier exploit disponible o módulo auxiliar en msfconsole, podemos poner en segundo plano la sesión siempre que formen un canal de comunicación con el host de destino. Esto se puede hacer presionando el botón `[CTRL] + [Z]` combinación de teclas o escribiendo el `background`comando en el caso de etapas Meterpreter. Esto nos mostrará un mensaje de confirmación. Después de aceptar el mensaje, volveremos al mensaje de msfconsole ( `msf6 >`) e inmediatamente podrá iniciar un módulo diferente.

#### Listado de sesiones activas
Podemos usar el `sessions` comando para ver nuestras sesiones actualmente activas.
```js
msf6 exploit(windows/smb/psexec_psh) > sessions

Active sessions
===============

  Id  Name  Type                     Information                 Connection
  --  ----  ----                     -----------                 ----------
  1         meterpreter x86/windows  NT AUTHORITY\SYSTEM @ MS01  10.10.10.129:443 -> 10.10.10.205:50501 (10.10.10.205)
```

#### Interactuar con una sesión
Puedes usar el `sessions -i [no.]` comando para abrir una sesión específica.
```js
msf6 exploit(windows/smb/psexec_psh) > sessions -i 1
[*] Starting interaction with 1...

meterpreter > 
```

Esto es especialmente útil cuando queremos ejecutar un módulo adicional en un sistema ya explotado con un canal de comunicación estable y formado.

## Jobs
Si, por ejemplo, estamos ejecutando un exploit activo en un puerto específico y necesitamos este puerto para un módulo diferente, no podemos simplemente terminar la sesión usando `[CTRL] + [C]`. Si hiciéramos eso, veríamos que el puerto aún estaría en uso, lo que afectaría nuestro uso del nuevo módulo. Entonces, en lugar de eso, necesitaríamos usar el `jobs` comando para ver las tareas actualmente activas que se ejecutan en segundo plano y finalizar las antiguas para liberar el puerto.

#### Visualización del menú de ayuda del comando Jobs
Podemos ver el menú de ayuda de este comando, como otros, escribiendo `jobs -h`.

```js
msf6 exploit(multi/handler) > jobs -h
Usage: jobs [options]

Active job manipulation and interaction.

OPTIONS:

    -K        Terminate all running jobs.
    -P        Persist all running jobs on restart.
    -S <opt>  Row search filter.
    -h        Help banner.
    -i <opt>  Lists detailed information about a running job.
    -k <opt>  Terminate jobs by job ID and/or range.
    -l        List all running jobs.
    -p <opt>  Add persistence to job by job ID
    -v        Print more detailed info.  Use with -i and -l
```

#### Visualización del menú de ayuda del comando de explotación

Cuando ejecutamos un exploit, podemos ejecutarlo como un trabajo escribiendo `exploit -j`. Según el menú de ayuda para el `exploit` comando, agregando `-j`a nuestro mando. en lugar de solo `exploit` o `run`, lo "ejecutará en el contexto de un trabajo".
```js
msf6 exploit(multi/handler) > exploit -h
Usage: exploit [options]

Launches an exploitation attempt.

OPTIONS:

    -J        Force running in the foreground, even if passive.
    -e <opt>  The payload encoder to use.  If none is specified, ENCODER is used.
    -f        Force the exploit to run regardless of the value of MinimumRank.
    -h        Help banner.
    -j        Run in the context of a job.
	
<SNIP
```

#### Ejecutar un exploit como trabajo en segundo plano
```js
msf6 exploit(multi/handler) > exploit -j
[*] Exploit running as background job 0.
[*] Exploit completed, but no session was created.

[*] Started reverse TCP handler on 10.10.14.34:4444
```

#### Listado de trabajos en ejecución
Para enumerar todos los trabajos en ejecución, podemos usar el `jobs -l`dominio. Para finalizar un trabajo específico, consulte el índice no. del trabajo y utilizar el `kill [index no.]`dominio. Utilizar el `jobs -K` comando para eliminar todos los trabajos en ejecución.

```js
msf6 exploit(multi/handler) > jobs -l
```

El objetivo tiene una aplicación web específica en ejecución que podemos encontrar buscando en el código fuente HTML. ¿Cómo se llama esa aplicación web?

```rb
┌──(pelado㉿kali)-[~/OSCP/HTB/Academy/Metasploit]
└─$ nmap -sCV --min-rate=1000 -Pn -n 10.129.203.52
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-24 19:08 EDT
Nmap scan report for 10.129.203.52
Host is up (0.18s latency).
Not shown: 988 closed tcp ports (conn-refused)
PORT     STATE    SERVICE        VERSION
7/tcp    filtered echo
22/tcp   open     ssh            OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 71:08:b0:c4:f3:ca:97:57:64:97:70:f9:fe:c5:0c:7b (RSA)
|   256 45:c3:b5:14:63:99:3d:9e:b3:22:51:e5:97:76:e1:50 (ECDSA)
|_  256 2e:c2:41:66:46:ef:b6:81:95:d5:aa:35:23:94:55:38 (ED25519)
80/tcp   open     http           Apache httpd 2.4.41 ((Ubuntu))
|_http-title: elFinder 2.1.x source version with PHP connector
|_http-server-header: Apache/2.4.41 (Ubuntu)
444/tcp  filtered snpp
902/tcp  filtered iss-realsecure
1058/tcp filtered nim
3005/tcp filtered deslogin
3517/tcp filtered 802-11-iapp
4006/tcp filtered pxc-spvr
4449/tcp filtered privatewire
5102/tcp filtered admeng
7741/tcp filtered scriptview
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

elFinder

Encuentre el exploit existente en MSF y utilícelo para obtener un shell en el objetivo. ¿Cuál es el nombre de usuario del usuario con el que obtuvo un shell?

www-data


El sistema de destino tiene una versión antigua de Sudo ejecutándose. Encuentre el exploit relevante y obtenga acceso de root al sistema de destino. Busque el archivo flag.txt y envíe su contenido como respuesta.