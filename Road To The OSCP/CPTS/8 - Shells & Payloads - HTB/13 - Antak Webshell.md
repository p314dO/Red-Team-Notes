## ASPX y un consejo de aprendizaje rápido
Antes de sumergirnos en los conceptos y ejercicios de aspx shell, debemos tomarnos el tiempo para cubrir un recurso de aprendizaje que puede ayudar a reforzar la mayoría de los conceptos cubiertos aquí en HTB Academy. En ocasiones, puede resultar un desafío visualizar un concepto utilizando un solo método de aprendizaje. Es bueno complementar la lectura viendo demostraciones y realizando prácticas como lo hemos estado haciendo hasta ahora. Los tutoriales en vídeo pueden ser una manera increíble de aprender conceptos, además se pueden consumir de manera informal (almorzando, acostado en la cama, sentado en el sofá, etc.). Un gran recurso para utilizar en el aprendizaje es `IPPSEC's`sitio de blogs [ippsec.rocks](https://ippsec.rocks/?#) . El sitio es una poderosa herramienta de aprendizaje. Tomemos, por ejemplo, el concepto de web shells. Podemos usar su sitio para escribir el concepto que queremos aprender, como aspx.

## ASPX
`Active Server Page Extended` ( `ASPX`) es un tipo/extensión de archivo escrito para [ASP.NET Framework de Microsoft](https://docs.microsoft.com/en-us/aspnet/overview) . En un servidor web que ejecuta el marco ASP.NET, se pueden generar páginas de formularios web para que los usuarios ingresen datos. Del lado del servidor, la información se convertirá a HTML. Podemos aprovechar esto utilizando un shell web basado en ASPX para controlar el sistema operativo Windows subyacente. Seamos testigos de esto de primera mano utilizando Antak Webshell.

## Antak Webshell
Antak es un shell web ASP.Net integrado incluido en el [proyecto Nishang](https://github.com/samratashok/nishang) . Nishang es un conjunto de herramientas ofensivas de PowerShell que puede brindar opciones para cualquier parte de su pentest. Dado que por el momento nos centramos en las aplicaciones web, mantengamos la vista puesta en `Antak`. Antak utiliza PowerShell para interactuar con el host, lo que lo hace ideal para adquirir un shell web en un servidor Windows. La interfaz de usuario incluso tiene un tema similar al de PowerShell. Es hora de sumergirse y experimentar con Antak.

Los archivos Antak se pueden encontrar en el `/usr/share/nishang/Antak-WebShell` directorio.
```js
ls /usr/share/nishang/Antak-WebShell
```

## Demostración de Antak
```js
cp /usr/share/nishang/Antak-WebShell/antak.aspx /home/pelado/HTB/Academy/ShellsPayloads/Upload.aspx
```

Despues de subir la shell, exactamente como en el modulo anterior...

![[2024-06-22_05-46.png]]


