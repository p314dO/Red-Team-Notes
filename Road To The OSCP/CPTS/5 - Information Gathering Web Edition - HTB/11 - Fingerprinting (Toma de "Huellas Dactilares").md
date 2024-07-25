La toma de huellas dactilares se centra en extraer detalles técnicos sobre las tecnologías que impulsan un sitio web o una aplicación web.

La toma de huellas dactilares sirve como piedra angular del reconocimiento web por varias razones:
- `Targeted Attacks`: Al conocer las tecnologías específicas en uso, los atacantes pueden centrar sus esfuerzos en exploits y vulnerabilidades que se sabe que afectan a esos sistemas. Esto aumenta significativamente las posibilidades de lograr un compromiso exitoso.
- `Identifying Misconfigurations`: La toma de huellas dactilares puede exponer software mal configurado u obsoleto, configuraciones predeterminadas u otras debilidades que podrían no ser evidentes mediante otros métodos de reconocimiento.
- `Prioritising Targets`: Cuando se enfrenta a múltiples objetivos potenciales, la toma de huellas digitales ayuda a priorizar los esfuerzos al identificar los sistemas que tienen más probabilidades de ser vulnerables o contener información valiosa.
- `Building a Comprehensive Profile`: La combinación de datos de huellas dactilares con otros hallazgos de reconocimiento crea una visión holística de la infraestructura del objetivo, lo que ayuda a comprender su postura de seguridad general y sus posibles vectores de ataque.

## Técnicas de toma de huellas dactilares
Existen varias técnicas utilizadas para la toma de huellas digitales de tecnología y servidores web:
- `Banner Grabbing`: La captura de banners implica analizar los banners presentados por los servidores web y otros servicios. Estos carteles suelen revelar el software del servidor, los números de versión y otros detalles.
- `Analysing HTTP Headers`: Los encabezados HTTP transmitidos con cada solicitud y respuesta de una página web contienen una gran cantidad de información. El `Server` El encabezado generalmente revela el software del servidor web, mientras que el `X-Powered-By` El encabezado puede revelar tecnologías adicionales como lenguajes de secuencias de comandos o marcos.
- `Probing for Specific Responses`: Enviar solicitudes especialmente diseñadas al objetivo puede generar respuestas únicas que revelen tecnologías o versiones específicas. Por ejemplo, ciertos mensajes de error o comportamientos son característicos de determinados servidores web o componentes de software.
- `Analysing Page Content`: El contenido de una página web, incluida su estructura, secuencias de comandos y otros elementos, a menudo puede proporcionar pistas sobre las tecnologías subyacentes. Puede haber un encabezado de derechos de autor que indique que se está utilizando un software específico, por ejemplo.

Existe una variedad de herramientas que automatizan el proceso de toma de huellas digitales, combinando varias técnicas para identificar servidores web, sistemas operativos, sistemas de gestión de contenidos y otras tecnologías:

| Herramienta  | Descripción                                                                                                                                                 | Características                                                                                                               |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `Wappalyzer` | Extensión de navegador y servicio en línea para creación de perfiles tecnológicos de sitios web.                                                            | Identifica una amplia gama de tecnologías web, incluidos CMS, marcos, herramientas de análisis y más.                         |
| `BuiltWith`  | Perfilador de tecnología web que proporciona informes detallados sobre la pila de tecnología de un sitio web.                                               | Ofrece planes gratuitos y de pago con distintos niveles de detalle.                                                           |
| `WhatWeb`    | Herramienta de línea de comandos para la toma de huellas digitales de sitios web.                                                                           | Utiliza una amplia base de datos de firmas para identificar diversas tecnologías web.                                         |
| `Nmap`       | Escáner de red versátil que se puede utilizar para diversas tareas de reconocimiento, incluidas las huellas digitales del sistema operativo y del servicio. | Se puede utilizar con scripts (NSE) para realizar tomas de huellas dactilares más especializadas.                             |
| `Netcraft`   | Ofrece una gama de servicios de seguridad web, que incluyen tomas de huellas digitales de sitios web e informes de seguridad.                               | Proporciona informes detallados sobre la tecnología de un sitio web, el proveedor de alojamiento y la situación de seguridad. |
| `wafw00f`    | Herramienta de línea de comandos diseñada específicamente para identificar firewalls de aplicaciones web (WAF).                                             | Ayuda a determinar si hay un WAF presente y, de ser así, su tipo y configuración.                                             |

### Banner Grabbing
```bash
curl -I inlanefreight.com
```

```js
p314d0@htb[/htb]$ curl -I inlanefreight.com

HTTP/1.1 301 Moved Permanently
Date: Fri, 31 May 2024 12:07:44 GMT
Server: Apache/2.4.41 (Ubuntu)
Location: https://inlanefreight.com/
Content-Type: text/html; charset=iso-8859-1
```

```js
p314d0@htb[/htb]$ curl -I https://inlanefreight.com

HTTP/1.1 301 Moved Permanently
Date: Fri, 31 May 2024 12:12:12 GMT
Server: Apache/2.4.41 (Ubuntu)
X-Redirect-By: WordPress
Location: https://www.inlanefreight.com/
Content-Type: text/html; charset=UTF-8
```

### wafw00f
`Web Application Firewalls` ( `WAFs`) son soluciones de seguridad diseñadas para proteger las aplicaciones web de diversos ataques. Antes de proceder con más huellas dactilares, es fundamental determinar si `inlanefreight.com` emplea un WAF, ya que podría interferir con nuestras sondas o potencialmente bloquear nuestras solicitudes.

```js
pip3 install git+https://github.com/EnableSecurity/wafw00f
```

```js
p314d0@htb[/htb]$ wafw00f inlanefreight.com
```

### Nikto
```bash
sudo apt update && sudo apt install -y perl
git clone https://github.com/sullo/nikto
cd nikto/program
chmod +x ./nikto.pl
```

```bash
nikto -h inlanefreight.com -Tuning b
```

El `-h`El indicador especifica el host de destino. El `-Tuning b` la bandera dice `Nikto` para ejecutar únicamente los módulos de Identificación de software. `Nikto` Luego iniciará una serie de pruebas, intentando identificar software obsoleto, archivos o configuraciones inseguros y otros riesgos potenciales de seguridad.

El escaneo de reconocimiento en `inlanefreight.com` revela varios hallazgos clave:
- `IPs`: El sitio web resuelve tanto en IPv4 ( `134.209.24.248`) e IPv6 ( `2a03:b0c0:1:e0::32c:b001`) direcciones.
- `Server Technology`: El sitio web se ejecuta en `Apache/2.4.41 (Ubuntu)`
- `WordPress Presence`: El escaneo identificó una instalación de WordPress, incluida la página de inicio de sesión ( `/wp-login.php`). Esto sugiere que el sitio podría ser un objetivo potencial para exploits comunes relacionados con WordPress.
- `Information Disclosure`: La presencia de un `license.txt` El archivo podría revelar detalles adicionales sobre los componentes de software del sitio web.
- `Headers`: Se encontraron varios encabezados no estándar o inseguros, incluido uno faltante `Strict-Transport-Security` encabezado y un potencialmente inseguro `x-redirect-by` encabezamiento.