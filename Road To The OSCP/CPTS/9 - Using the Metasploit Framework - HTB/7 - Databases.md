`Databases` en `msfconsole`se utilizan para realizar un seguimiento de sus resultados. `Msfconsole`tiene soporte incorporado para el sistema de base de datos PostgreSQL. Con él, tenemos acceso directo, rápido y fácil a los resultados del escaneo con la capacidad adicional de importar y exportar resultados junto con herramientas de terceros.
## Configurando la base de datos
```shell-session
sudo service postgresql status
```

#### Iniciar PostgreSQL
```shell-session
p314d0@htb[/htb]$ sudo systemctl start postgresql
```

#### MSF: iniciar una base de datos
```js
p314d0@htb[/htb]$ sudo msfdb init

[i] Database already started
[+] Creating database user 'msf'
[+] Creating databases 'msf'
[+] Creating databases 'msf_test'
[+] Creating configuration file '/usr/share/metasploit-framework/config/database.yml'
[+] Creating initial database schema
rake aborted!
NoMethodError: undefined method `without' for #<Bundler::Settings:0x000055dddcf8cba8>
Did you mean? with_options

<SNIP>
```

#### MSF: conectarse a la base de datos iniciada

```shell-session
p314d0@htb[/htb]$ sudo msfdb run
```

Sin embargo, si ya tenemos la base de datos configurada y no podemos cambiar la contraseña al nombre de usuario de MSF, procedemos con estos comandos:

#### MSF: reiniciar la base de datos
```js
p314d0@htb[/htb]$ msfdb reinit
p314d0@htb[/htb]$ cp /usr/share/metasploit-framework/config/database.yml ~/.msf4/
p314d0@htb[/htb]$ sudo service postgresql restart
p314d0@htb[/htb]$ msfconsole -q

msf6 > db_status

[*] Connected to msf. Connection type: PostgreSQL.
```

```
msf6 > help database

Database Backend Commands
=========================

    Command           Description
    -------           -----------
    db_connect        Connect to an existing database
    db_disconnect     Disconnect from the current database instance
    db_export         Export a file containing the contents of the database
    db_import         Import a scan result file (filetype will be auto-detected)
    db_nmap           Executes nmap and records the output automatically
    db_rebuild_cache  Rebuilds the database-stored module cache
    db_status         Show the current database status
    hosts             List all hosts in the database
    loot              List all loot in the database
    notes             List all notes in the database
    services          List all services in the database
    vulns             List all vulnerabilities in the database
    workspace         Switch between database workspaces
	

msf6 > db_status

[*] Connected to msf. Connection type: postgresql.
```

#### Espacios de trabajo

podemos pensar en `Workspaces`de la misma manera que pensaríamos en las carpetas de un proyecto. Podemos segregar los diferentes resultados del escaneo, hosts e información extraída por IP, subred, red o dominio.

```js
msf6 > workspace
* default
msf6 > workspace -a Target_1
[*] Added workspace: Target_1
[*] Workspace: Target_1
msf6 > workspace
  default
* Target_1
msf6 > 
```

Para ver qué más podemos hacer con Workspaces, podemos usar el `workspace -h` Comando para el menú de ayuda relacionado con los espacios de trabajo.
```shell-session
msf6 > workspace -h

Usage:
    workspace                  List workspaces
    workspace -v               List workspaces verbosely
    workspace [name]           Switch workspace
    workspace -a [name] ...    Add workspace(s)
    workspace -d [name] ...    Delete workspace(s)
    workspace -D               Delete all workspaces
    workspace -r     Rename workspace
    workspace -h               Show this help information
```

## Importación de resultados de escaneo
#### Escaneo de Nmap almacenado
```shell-session
p314d0@htb[/htb]$ cat Target.nmap

Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-17 20:54 UTC
Nmap scan report for 10.10.10.40
Host is up (0.017s latency).
Not shown: 991 closed ports
PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49156/tcp open  msrpc        Microsoft Windows RPC
49157/tcp open  msrpc        Microsoft Windows RPC
Service Info: Host: HARIS-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 60.81 seconds
```

```shell-session
msf6 > db_import Target.xml
```

## Usando Nmap dentro de MSFconsole
#### MSF-Nmap
```shell-session
msf6 > db_nmap -sV -sS 10.10.10.8
```

## Data Backup
#### MSF - Exportación de base de datos
```shell-session
msf6 > db_export -h
```

