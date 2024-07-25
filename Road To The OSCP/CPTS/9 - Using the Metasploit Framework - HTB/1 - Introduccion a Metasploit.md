El `Metasploit Project`es una plataforma de prueba de penetración modular basada en Ruby que le permite escribir, probar y ejecutar el código de explotación. Este código de exploit puede ser personalizado por el usuario o tomado de una base de datos que contenga los últimos exploits modularizados y ya descubiertos. El `Metasploit Framework`incluye un conjunto de herramientas que puede utilizar para probar vulnerabilidades de seguridad, enumerar redes, ejecutar ataques y evadir la detección. En esencia, el `Metasploit Project` es una colección de herramientas de uso común que proporcionan un entorno completo para pruebas de penetración y desarrollo de exploits.

![](https://academy.hackthebox.com/storage/modules/39/S02_SS01.png)

De forma predeterminada, todos los archivos base relacionados con Metasploit Framework se pueden encontrar en `/usr/share/metasploit-framework`

#### Data, Documentation, Lib
Estos son los archivos base del Framework. Los datos y la biblioteca son las partes funcionales de la interfaz msfconsole, mientras que la carpeta Documentación contiene todos los detalles técnicos sobre el proyecto.

#### Modules
Los módulos detallados anteriormente se dividen en categorías separadas en esta carpeta.
```js
p314d0@htb[/htb]$ ls /usr/share/metasploit-framework/modules

auxiliary  encoders  evasion  exploits  nops  payloads  post
```

#### Plugins
Los complementos ofrecen al pentester más flexibilidad al usar el `msfconsole` ya que se pueden cargar fácilmente de forma manual o automática según sea necesario para proporcionar funcionalidad y automatización adicionales durante nuestra evaluación.
```js
p314d0@htb[/htb]$ ls /usr/share/metasploit-framework/plugins/

aggregator.rb      ips_filter.rb  openvas.rb           sounds.rb
alias.rb           komand.rb      pcap_log.rb          sqlmap.rb
auto_add_route.rb  lab.rb         request.rb           thread.rb
beholder.rb        libnotify.rb   rssfeed.rb           token_adduser.rb
db_credcollect.rb  msfd.rb        sample.rb            token_hunter.rb
db_tracker.rb      msgrpc.rb      session_notifier.rb  wiki.rb
event_tester.rb    nessus.rb      session_tagger.rb    wmap.rb
ffautoregen.rb     nexpose.rb     socket_logger.rb
```

#### Scripts
Funcionalidad de Meterpreter y otros scripts útiles.
```js
p314d0@htb[/htb]$ ls /usr/share/metasploit-framework/scripts/

meterpreter  ps  resource  shell
```

#### Tools
Utilidades de línea de comandos que se pueden llamar directamente desde el `msfconsole` menú.
```js
p314d0@htb[/htb]$ ls /usr/share/metasploit-framework/tools/

context  docs     hardware  modules   payloads
dev      exploit  memdump   password  recon
```

Which version of Metasploit comes equipped with a GUI interface?


What command do you use to interact with the free version of Metasploit?
msfconsole