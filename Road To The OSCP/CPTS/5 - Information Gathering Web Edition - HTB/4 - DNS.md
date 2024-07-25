El `Domain Name System` ( `DNS`) actúa como el GPS de Internet y guía su viaje en línea desde puntos de referencia memorables (nombres de dominio) hasta coordenadas numéricas precisas (direcciones IP). Al igual que el GPS traduce el nombre de un destino en latitud y longitud para la navegación, el DNS traduce nombres de dominio legibles por humanos (como `www.example.com`) en las direcciones IP numéricas (como `192.0.2.1`) que utilizan las computadoras para comunicarse.

![](https://mermaid.ink/svg/pako:eNptkk1uwjAQha8y8rpcIItWkAAtUNQmlSrksDDxlEQQT-QfJIS4ex2nNG1arzx-n5-ex3NhBUlkEdtr0ZSwSnMFfhm36w425DTEVDfOou60do15XGJxMBCLosQtjEb3MLk8vcCMnJIP156ceA3WFIiYZ6ikgWSdwatDfQZLkKKh4wn1dnBngyZceuQxKYWFNS39jjtTWfyCvdsgb2t9c-wN4-CU_A7dy8mPjFOeYuG0qU4IK6KDa4bgLdjck9ZpZcC_20e7dWmYbRroGU-JLKxFjZCh7h88C_KCv62Sf9RFUJd87GxJurLCtsH-cssuUlfMu8blit2xGnUtKul_-NKKObMl1pizyG-l0Iec5erqOeEsZWdVsMhqh3dMk9uXLPoQR-Mr10hhMamE73L9fYqysqSfuwEKc3T9BOe0sj4)

## Cómo funciona el DNS
1. `Your Computer Asks for Directions (DNS Query)`: Cuando ingresa el nombre de dominio, su computadora primero verifica su memoria (caché) para ver si recuerda la dirección IP de una visita anterior. De lo contrario, se comunica con un solucionador de DNS, generalmente proporcionado por su proveedor de servicios de Internet (ISP).
2. `The DNS Resolver Checks its Map (Recursive Lookup)`: El solucionador también tiene un caché y, si no encuentra la dirección IP allí, inicia un viaje a través de la jerarquía DNS. Comienza preguntando a un servidor de nombres raíz, que es como el bibliotecario de Internet.
3. `Root Name Server Points the Way`: El servidor raíz no conoce la dirección exacta, pero sabe quién la conoce: el servidor de nombres del dominio de nivel superior (TLD) responsable de la terminación del dominio (por ejemplo, .com, .org). Apunta al solucionador en la dirección correcta.
4. `TLD Name Server Narrows It Down`: El servidor de nombres de TLD es como un mapa regional. Sabe qué servidor de nombres autorizado es responsable del dominio específico que estás buscando (por ejemplo, `example.com`) y envía el solucionador allí.
5. `Authoritative Name Server Delivers the Address`: El servidor de nombres autorizado es la última parada. Es como la dirección postal del sitio web que desea. Mantiene la dirección IP correcta y la envía de vuelta al solucionador.
6. `The DNS Resolver Returns the Information`: El solucionador recibe la dirección IP y se la da a su computadora. También lo recuerda por un tiempo (lo almacena en caché), en caso de que quieras volver a visitar el sitio web pronto.
7. `Your Computer Connects`: Ahora que su computadora conoce la dirección IP, puede conectarse directamente al servidor web que aloja el sitio web y puede comenzar a navegar.

### El archivo de hosts
El archivo `hosts` es un archivo de texto simple que se utiliza para asignar nombres de host a direcciones IP, lo que proporciona un método manual de resolución de nombres de dominio que evita el proceso DNS. El archivo `hosts` se encuentra en `C:\Windows\System32\drivers\etc\hosts` en Windows y en `/etc/hosts`en Linux y MacOS. Cada línea del archivo sigue el formato:

```txt
<IP Address>    <Hostname> [<Alias> ...]
```

Por ejemplo:
```txt
127.0.0.1       localhost
192.168.1.10    devserver.local
```

### Conceptos clave de DNS
En el `Domain Name System` ( `DNS`), a `zone`es una parte distinta del espacio de nombres de dominio que administra una entidad o administrador específico. Piense en ello como un contenedor virtual para un conjunto de nombres de dominio. Por ejemplo, `example.com` y todos sus subdominios (como `mail.example.com` o `blog.example.com`) normalmente pertenecería a la misma zona DNS. El archivo de zona, un archivo de texto que reside en un servidor DNS, define los registros de recursos (que se analizan a continuación) dentro de esta zona, proporcionando información crucial para traducir nombres de dominio en direcciones IP.
Para ilustrar, aquí hay un ejemplo simplificado de lo que es un archivo de zona, por ejemplo. `example.com` podría verse así:

```js
$TTL 3600 ; Default Time-To-Live (1 hour)
@       IN SOA   ns1.example.com. admin.example.com. (
                2024060401 ; Serial number (YYYYMMDDNN)
                3600       ; Refresh interval
                900        ; Retry interval
                604800     ; Expire time
                86400 )    ; Minimum TTL

@       IN NS    ns1.example.com.
@       IN NS    ns2.example.com.
@       IN MX 10 mail.example.com.
www     IN A     192.0.2.1
mail    IN A     198.51.100.1
ftp     IN CNAME www.example.com.
```

Este archivo define los servidores de nombres autorizados ( `NS` registros ), servidor de correo ( `MX` registro ) y direcciones IP ( `A` registros ) para varios hosts dentro del `example.com` dominio. Los servidores DNS almacenan varios registros de recursos, cada uno de los cuales tiene un propósito específico en el proceso de resolución de nombres de dominio. Exploremos algunos de los conceptos de DNS más comunes:

|Concepto DNS|Descripción|Ejemplo|
|---|---|---|
|`Domain Name`|Una etiqueta legible por humanos para un sitio web u otro recurso de Internet.|`www.example.com`|
|`IP Address`|Un identificador numérico único asignado a cada dispositivo conectado a Internet.|`192.0.2.1`|
|`DNS Resolver`|Un servidor que traduce nombres de dominio en direcciones IP.|El servidor DNS de su ISP o solucionadores públicos como Google DNS ( `8.8.8.8`)|
|`Root Name Server`|Los servidores de nivel superior en la jerarquía DNS.|Hay 13 servidores raíz en todo el mundo, denominados AM: `a.root-servers.net`|
|`TLD Name Server`|Servidores responsables de dominios de nivel superior específicos (por ejemplo, .com, .org).|[Verisign](https://en.wikipedia.org/wiki/Verisign) para `.com`, [PIR](https://en.wikipedia.org/wiki/Public_Interest_Registry) para `.org`|
|`Authoritative Name Server`|El servidor que contiene la dirección IP real de un dominio.|A menudo lo gestionan proveedores de alojamiento o registradores de dominios.|
|`DNS Record Types`|Diferentes tipos de información almacenada en DNS.|A, AAAA, CNAME, MX, NS, TXT, etc.|
Ahora que hemos explorado los conceptos fundamentales de DNS, profundicemos en los componentes básicos de la información de DNS: los distintos tipos de registros. Estos registros almacenan diferentes tipos de datos asociados con los nombres de dominio, cada uno de los cuales tiene un propósito específico:

|Tipo de registro|Nombre completo|Descripción|Ejemplo de archivo de zona|
|---|---|---|---|
|`A`|Registro de dirección|Asigna un nombre de host a su dirección IPv4.|`www.example.com.` EN UN `192.0.2.1`|
|`AAAA`|Registro de direcciones IPv6|Asigna un nombre de host a su dirección IPv6.|`www.example.com.` EN AAAA `2001:db8:85a3::8a2e:370:7334`|
|`CNAME`|Registro de nombre canónico|Crea un alias para un nombre de host, apuntándolo a otro nombre de host.|`blog.example.com.` EN NOMBRE `webserver.example.net.`|
|`MX`|Registro de intercambio de correo|Especifica los servidores de correo responsables de manejar el correo electrónico del dominio.|`example.com.` EN MX 10 `mail.example.com.`|
|`NS`|Registro del servidor de nombres|Delega una zona DNS a un servidor de nombres autorizado específico.|`example.com.` EN NS `ns1.example.com.`|
|`TXT`|Registro de texto|Almacena información de texto arbitraria, que a menudo se utiliza para la verificación de dominios o políticas de seguridad.|`example.com.` EN TEXTO `"v=spf1 mx -all"` (registro SPF)|
|`SOA`|Inicio del Registro de Autoridad|Especifica información administrativa sobre una zona DNS, incluido el servidor de nombres principal, el correo electrónico de la persona responsable y otros parámetros.|`example.com.` EN SOA `ns1.example.com. admin.example.com. 2024060301 10800 3600 604800 86400`|
|`SRV`|Registro de servicio|Define el nombre de host y el número de puerto para servicios específicos.|`_sip._udp.example.com.` EN SRV 10 5 5060 `sipserver.example.com.`|
|`PTR`|Registro de puntero|Se utiliza para búsquedas de DNS inversas, asignando una dirección IP a un nombre de host.|`1.2.0.192.in-addr.arpa.` EN PTR `www.example.com.`|
El " `IN`" en los ejemplos significa "Internet". Es un campo de clase en los registros DNS que especifica la familia de protocolos. En la mayoría de los casos, verá " `IN`" se utiliza, ya que denota el conjunto de protocolos de Internet (IP) utilizado para la mayoría de los nombres de dominio. Existen otros valores de clase (p. ej., `CH` para Caosnet, `HS` para Hesiod) pero rara vez se utilizan en configuraciones DNS modernas. En esencia, " `IN`" es simplemente una convención que indica que el registro se aplica a los protocolos de Internet estándar que utilizamos hoy en día. Si bien puede parecer un detalle adicional, comprender su significado proporciona una comprensión más profunda de la estructura del registro DNS.

## Por qué es importante el DNS para Web Recon
DNS no es simplemente un protocolo técnico para traducir nombres de dominio; Es un componente crítico de la infraestructura de un objetivo que se puede aprovechar para descubrir vulnerabilidades y obtener acceso durante una prueba de penetración:
- `Uncovering Assets`: Los registros DNS pueden revelar una gran cantidad de información, incluidos subdominios, servidores de correo y registros de servidores de nombres. Por ejemplo, un `CNAME` registro que apunta a un servidor obsoleto ( `dev.example.com` CNOMBRE `oldserver.example.net`) podría conducir a un sistema vulnerable.
- `Mapping the Network Infrastructure`: Puede crear un mapa completo de la infraestructura de red del objetivo analizando los datos DNS. Por ejemplo, identificar los servidores de nombres ( `NS` registros) para un dominio puede revelar el proveedor de alojamiento utilizado, mientras que un `A` grabar para `loadbalancer.example.com`puede identificar un equilibrador de carga. Esto le ayuda a comprender cómo están conectados los diferentes sistemas, identificar el flujo de tráfico e identificar posibles puntos críticos o debilidades que podrían explotarse durante una prueba de penetración.
- `Monitoring for Changes`: La supervisión continua de los registros DNS puede revelar cambios en la infraestructura del objetivo a lo largo del tiempo. Por ejemplo, la aparición repentina de un nuevo subdominio ( `vpn.example.com`) podría indicar un nuevo punto de entrada a la red, mientras que un `TXT` registro que contiene un valor como `_1password=...` sugiere firmemente que la organización está utilizando 1Password, que podría aprovecharse para ataques de ingeniería social o campañas de phishing dirigidas.