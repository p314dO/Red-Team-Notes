
Cada aplicación que admite mecanismos de autenticación compara las entradas/credenciales proporcionadas con bases de datos locales o remotas. En el caso de bases de datos locales, estas credenciales se almacenan localmente en el sistema. Las aplicaciones web suelen ser vulnerables a las inyecciones de SQL, lo que puede llevar al peor de los casos a que los atacantes vean la totalidad de los datos de una organización en texto sin formato.

==Todos los sistemas== operativos admiten este tipo de mecanismos de autenticación. Por tanto, las credenciales almacenadas se almacenan localmente. Veamos con más detalle cómo se crean, almacenan y administran los sistemas basados ​​en Windows y Linux.

## Linux
Como ya sabemos, los sistemas basados ​​en Linux manejan todo en forma de archivo. En consecuencia, las contraseñas también se almacenan cifradas en un archivo. Este archivo se llama `shadow` archivo y se encuentra en `/etc/shadow`y es parte del sistema de gestión de usuarios de Linux. Además, estas contraseñas se almacenan comúnmente en forma de `hashes`. Un ejemplo puede verse así:

```js
root@htb:~# cat /etc/shadow

...SNIP...
htb-student:$y$j9T$3QSBB6CbHEu...SNIP...f8Ms:18955:0:99999:7:::
```

El `/etc/shadow` El archivo tiene un formato único en el que las entradas se ingresan y guardan cuando se crean nuevos usuarios.

|   |   |   |   |   |   |   |   |   |
|---|---|---|---|---|---|---|---|---|
|htb-estudiante:|$y$j9T$3QSBB6CbHEu...SNIP...f8Ms:|18955:|0:|99999:|7:|:|:|:|
|`<username>`:|`<encrypted password>`:|`<day of last change>`:|`<min age>`:|`<max age>`:|`<warning period>`:|`<inactivity period>`:|`<expiration date>`:|`<reserved field>`|
El cifrado de la contraseña en este archivo tiene el siguiente formato:

|   |   |   |
|---|---|---|
|`$ <id>`|`$ <salt>`|`$ <hashed>`|
|`$ y`|`$ j9T`|`$ 3QSBB6CbHEu...SNIP...f8Ms`|
El tipo ( `id`) es el método hash criptográfico utilizado para cifrar la contraseña. En el pasado se utilizaron muchos métodos hash criptográficos diferentes y algunos sistemas todavía los utilizan en la actualidad.

| **ID**   | **Cryptographic Hash Algorithm**                                      |
| -------- | --------------------------------------------------------------------- |
| `$1$`    | [MD5](https://en.wikipedia.org/wiki/MD5)                              |
| `$2a$`   | [Blowfish](https://en.wikipedia.org/wiki/Blowfish_(cipher))           |
| `$5$`    | [SHA-256](https://en.wikipedia.org/wiki/SHA-2)                        |
| `$6$`    | [SHA-512](https://en.wikipedia.org/wiki/SHA-2)                        |
| `$sha1$` | [SHA1crypt](https://en.wikipedia.org/wiki/SHA-1)                      |
| `$y$`    | [Yescrypt](https://github.com/openwall/yescrypt)                      |
| `$gy$`   | [Gost-yescrypt](https://www.openwall.com/lists/yescrypt/2019/06/30/1) |
| `$7$`    | [Scrypt](https://en.wikipedia.org/wiki/Scrypt)                        |
Sin embargo, algunos archivos más pertenecen al sistema de gestión de usuarios de Linux. Los otros dos archivos son `/etc/passwd` y `/etc/group`. En el pasado, la contraseña cifrada se almacenaba junto con el nombre de usuario en el `/etc/passwd`archivo, pero esto se reconoció cada vez más como un problema de seguridad porque el archivo puede ser visto por todos los usuarios del sistema y debe ser legible. El `/etc/shadow` El archivo sólo puede ser leído por el usuario. `root`.

```js
p314d0@htb[/htb]$ cat /etc/passwd

...SNIP...
htb-student:x:1000:1000:,,,:/home/htb-student:/bin/bash
```

|   |   |   |   |   |   |   |
|---|---|---|---|---|---|---|
|`htb-student:`|`x:`|`1000:`|`1000:`|`,,,:`|`/home/htb-student:`|`/bin/bash`|
|`<username>:`|`<password>:`|`<uid>:`|`<gid>:`|`<comment>:`|`<home directory>:`|`<cmd executed after logging in>`|
La x en el campo de contraseña indica que la contraseña cifrada está en el archivo /etc/shadow. Sin embargo, la redirección al fichero /etc/shadow no hace invulnerables a los usuarios del sistema, ya que si los derechos de este fichero están mal configurados, el fichero puede ser manipulado para que el usuario root no necesite teclear una contraseña para entrar. Por lo tanto, un campo vacío significa que podemos iniciar sesión con el nombre de usuario sin introducir una contraseña.


## Proceso de autenticación de Windows
El [proceso de autenticación de clientes de Windows](https://docs.microsoft.com/en-us/windows-server/security/windows-authentication/credentials-processes-in-windows-authentication) a menudo puede ser más complicado que con los sistemas Linux y consta de muchos módulos diferentes que realizan todos los procesos de inicio de sesión, recuperación y verificación. Además, existen muchos procedimientos de autenticación diferentes y complejos en el sistema Windows, como la autenticación Kerberos. La [Autoridad de Seguridad Local](https://learn.microsoft.com/en-us/windows-server/security/credentials-protection-and-management/configuring-additional-lsa-protection) ( `LSA`) es un subsistema protegido que autentica a los usuarios y los registra en la computadora local. Además, la LSA mantiene información sobre todos los aspectos de la seguridad local en una computadora. También proporciona varios servicios para traducir entre nombres e identificaciones de seguridad ( `SIDs`).

El subsistema de seguridad realiza un seguimiento de las políticas y cuentas de seguridad que residen en un sistema informático. En el caso de un Controlador de Dominio, estas políticas y cuentas se aplican al dominio donde se encuentra el Controlador de Dominio. Estas políticas y cuentas se almacenan en Active Directory. Además, el subsistema LSA proporciona servicios para verificar el acceso a objetos, verificar los permisos de los usuarios y generar mensajes de monitoreo.

![image](https://academy.hackthebox.com/storage/modules/147/Auth_process1.png)

El inicio de sesión interactivo local se realiza mediante la interacción entre el proceso de inicio de sesión ( [WinLogon](https://www.microsoftpressstore.com/articles/article.aspx?p=2228450&seqNum=8) ), el proceso de interfaz de usuario de inicio de sesión ( `LogonUI`), el `credential providers`, `LSASS`, uno o mas `authentication packages`, y `SAM` o `Active Directory`. Los paquetes de autenticación, en este caso, son las bibliotecas de vínculos dinámicos ( `DLLs`) que realizan comprobaciones de autenticación. Por ejemplo, para inicios de sesión interactivos y no unidos a un dominio, el paquete de autenticación `Msv1_0.dll` se utiliza.

`Winlogon`es un proceso confiable responsable de gestionar las interacciones de los usuarios relacionadas con la seguridad. Éstas incluyen:

- Iniciar LogonUI para ingresar contraseñas al iniciar sesión
- Cambiar contraseñas
- Bloquear y desbloquear la estación de trabajo

Winlogon es el único proceso que intercepta las solicitudes de inicio de sesión desde el teclado enviadas mediante un mensaje RPC desde Win32k.sys. Winlogon inicia inmediatamente la aplicación LogonUI al iniciar sesión para mostrar la interfaz de usuario para iniciar sesión. Después de que Winlogon obtiene un nombre de usuario y una contraseña de los proveedores de credenciales, llama a LSASS para autenticar al usuario que intenta iniciar sesión.

#### LSASS

[Servicio del Subsistema de Autoridad de Seguridad Local](https://en.wikipedia.org/wiki/Local_Security_Authority_Subsystem_Service) ( `LSASS`) es una colección de muchos módulos y tiene acceso a todos los procesos de autenticación que se pueden encontrar en `%SystemRoot%\System32\Lsass.exe`. Este servicio es responsable de la política de seguridad del sistema local, la autenticación de usuarios y el envío de registros de auditoría de seguridad al `Event log`. En otras palabras, es la bóveda para los sistemas operativos basados ​​en Windows, y podemos encontrar una ilustración más detallada de la arquitectura LSASS [aquí](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-2000-server/cc961760(v=technet.10)?redirectedfrom=MSDN) .

| **Paquetes de autenticación** | **Descripción**                                                                                                                                                                                                                                       |
| ----------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Lsasrv.dll`                  | El servicio LSA Server aplica políticas de seguridad y actúa como administrador de paquetes de seguridad para LSA. El LSA contiene la función Negociar, que selecciona el protocolo NTLM o Kerberos después de determinar qué protocolo tendrá éxito. |
| `Msv1_0.dll`                  | Paquete de autenticación para inicios de sesión en máquinas locales que no requieren autenticación personalizada.                                                                                                                                     |
| `Samsrv.dll`                  | El Administrador de cuentas de seguridad (SAM) almacena cuentas de seguridad locales, aplica políticas almacenadas localmente y admite API.                                                                                                           |
| `Kerberos.dll`                | Paquete de seguridad cargado por LSA para la autenticación basada en Kerberos en una máquina.                                                                                                                                                         |
| `Netlogon.dll`                | Servicio de inicio de sesión basado en red.                                                                                                                                                                                                           |
| `Ntdsa.dll`                   | Esta biblioteca se utiliza para crear nuevos registros y carpetas en el registro de Windows.                                                                                                                                                          |
#### SAM Database
The [Security Account Manager](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc756748(v=ws.10)?redirectedfrom=MSDN) (`SAM`) is a database file in Windows operating systems that stores users' passwords. Se puede utilizar para autenticar usuarios locales y remotos. SAM utiliza medidas criptográficas para evitar que usuarios no autenticados accedan al sistema. Las contraseñas de usuario se almacenan en formato hash en una estructura de registro como `LM` hachís o un `NTLM`picadillo. Este archivo se encuentra en `%SystemRoot%/system32/config/SAM`y está montado en HKLM/SAM. Se requieren permisos a nivel de SISTEMA para verlo.

Los sistemas Windows se pueden asignar a un grupo de trabajo o a un dominio durante la instalación. Si el sistema ha sido asignado a un grupo de trabajo, maneja la base de datos SAM localmente y almacena a todos los usuarios existentes localmente en esta base de datos. Sin embargo, si el sistema se ha unido a un dominio, el controlador de dominio ( `DC`) debe validar las credenciales de la base de datos de Active Directory ( `ntds.dit`), que se almacena en `%SystemRoot%\ntds.dit`.

![](https://academy.hackthebox.com/storage/modules/147/authn_credman_credprov.png)

Credential Manager es una función integrada en todos los sistemas operativos Windows que permite a los usuarios guardar las credenciales que utilizan para acceder a diversos recursos de red y sitios web. Las credenciales guardadas se almacenan en función de los perfiles de usuario en la cuenta de cada usuario. `Credential Locker`. Las credenciales se cifran y almacenan en la siguiente ubicación:
```js
PS C:\Users\[Username]\AppData\Local\Microsoft\[Vault/Credentials]\
```

#### NTDS
Es muy común encontrarnos con entornos de red donde los sistemas Windows están unidos a un dominio de Windows. Esto es común porque facilita a los administradores la gestión de todos los sistemas propiedad de sus respectivas organizaciones (administración centralizada). En estos casos, los sistemas Windows enviarán todas las solicitudes de inicio de sesión a controladores de dominio que pertenecen al mismo bosque de Active Directory. Cada controlador de dominio aloja un archivo llamado `NTDS.dit`que se mantiene sincronizado en todos los controladores de dominio con la excepción de [los controladores de dominio de solo lectura](https://docs.microsoft.com/en-us/windows/win32/ad/rodc-and-active-directory-schema) . NTDS.dit es un archivo de base de datos que almacena los datos en Active Directory, que incluye, entre otros:
- User accounts (username & password hash)
- Group accounts
- Computer accounts
- Group policy objects

