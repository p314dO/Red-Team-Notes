WHOIS es un protocolo de consulta y respuesta ampliamente utilizado diseñado para acceder a bases de datos que almacenan información sobre recursos de Internet registrados.

```js
whois URL
```

Cada registro de WHOIS normalmente contiene la siguiente información:
- `Domain Name`: El nombre de dominio en sí (p. ej., ejemplo.com)
- `Registrar`: La empresa donde se registró el dominio (por ejemplo, GoDaddy, Namecheap)
- `Registrant Contact`: La persona u organización que registró el dominio.
- `Administrative Contact`: La persona responsable de gestionar el dominio.
- `Technical Contact`: La persona que maneja los problemas técnicos relacionados con el dominio.
- `Creation and Expiration Dates`: Cuándo se registró el dominio y cuándo expirará.
- `Name Servers`: Servidores que traducen el nombre de dominio en una dirección IP.

## Por qué WHOIS es importante para Web Recon
Los datos de WHOIS sirven como un tesoro de información para los evaluadores de penetración durante la fase de reconocimiento de una evaluación. Ofrece información valiosa sobre la huella digital de la organización objetivo y sus posibles vulnerabilidades:
- `Identifying Key Personnel`: Los registros de WHOIS a menudo revelan los nombres, direcciones de correo electrónico y números de teléfono de las personas responsables de administrar el dominio. Esta información se puede aprovechar para ataques de ingeniería social o para identificar objetivos potenciales para campañas de phishing.
- `Discovering Network Infrastructure`: Los detalles técnicos como servidores de nombres y direcciones IP proporcionan pistas sobre la infraestructura de red del objetivo. Esto puede ayudar a los evaluadores de penetración a identificar posibles puntos de entrada o configuraciones erróneas.
- `Historical Data Analysis`: Acceder a registros históricos de WHOIS a través de servicios como [WhoisFreaks](https://whoisfreaks.com/) puede revelar cambios de propiedad, información de contacto o detalles técnicos a lo largo del tiempo. Esto puede resultar útil para seguir la evolución de la presencia digital del objetivo.