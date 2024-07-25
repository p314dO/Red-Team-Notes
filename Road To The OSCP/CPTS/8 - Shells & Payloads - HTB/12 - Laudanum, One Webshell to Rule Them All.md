Laudanum es un repositorio de archivos listos para usar que se pueden usar para inyectar en una víctima y recibir acceso nuevamente a través de un shell inverso, ejecutar comandos en el host de la víctima directamente desde el navegador y más. El repositorio incluye archivos inyectables para muchos lenguajes de aplicaciones web diferentes para incluir `asp, aspx, jsp, php,`y más.

```js
┌──(pelado㉿kali)-[~/HTB/Academy/ShellsPayloads]
└─$ ls /usr/share/laudanum   
asp  aspx  cfm  helpers  jsp  php  wordpress
```

Los archivos de Laudanum se pueden encontrar en el `/usr/share/laudanum`directorio. Para la mayoría de los archivos dentro de Laudanum, puede copiarlos tal cual y colocarlos donde necesite que la víctima los ejecute. Para archivos específicos como los shells, primero debe editar el archivo para insertar su `attacking`dirección IP del host para garantizar que pueda acceder al shell web o recibir una devolución de llamada en el caso de que utilice un shell inverso.

#### Mover una copia para modificarla
```js
cp /usr/share/laudanum/aspx/shell.aspx /home/tester/demo.aspx
```

Añade tu dirección IP al `allowedIps` variable en línea `59`. Realice cualquier otro cambio que desee. Puede ser prudente eliminar el arte ASCII y los comentarios del archivo. Estos elementos en una carga útil a menudo están firmados y pueden alertar a los defensores/AV sobre lo que estás haciendo.

![[2024-06-22_03-53.png]]

![[2024-06-22_03-57.png]]
![[2024-06-22_04-08.png]]
Ahora podemos utilizar el shell Laudanum que subimos para enviar comandos al host. Podemos ver en el ejemplo que el `systeminfo` Se ejecutó el comando.