Una vez que el DNS dirige el tráfico al servidor correcto, la configuración del servidor web se vuelve crucial para determinar cómo se manejan las solicitudes entrantes. Los servidores web como Apache, Nginx o IIS están diseñados para alojar múltiples sitios web o aplicaciones en un solo servidor. Lo logran a través del alojamiento virtual, que les permite diferenciar entre dominios, subdominios o incluso sitios web separados con contenido distinto.

`Subdomains`: Estas son extensiones de un nombre de dominio principal (por ejemplo, `blog.example.com` es un subdominio de `example.com`). `Subdomains` normalmente tienen su propio `DNS records`, apuntando a la misma dirección IP que el dominio principal o a una diferente. Se pueden utilizar para organizar diferentes secciones o servicios de un sitio web.

`Virtual Hosts` ( `VHosts`): Los hosts virtuales son configuraciones dentro de un servidor web que permiten alojar múltiples sitios web o aplicaciones en un solo servidor. Se pueden asociar con dominios de nivel superior (por ejemplo, `example.com`) o subdominios (p. ej., `dev.example.com`). Cada host virtual puede tener su propia configuración independiente, lo que permite un control preciso sobre cómo se manejan las solicitudes.

Los hosts virtuales también se pueden configurar para utilizar diferentes dominios, no sólo subdominios. Por ejemplo:
```js
# Example of name-based virtual host configuration in Apache
<VirtualHost *:80>
    ServerName www.example1.com
    DocumentRoot /var/www/example1
</VirtualHost>

<VirtualHost *:80>
    ServerName www.example2.org
    DocumentRoot /var/www/example2
</VirtualHost>

<VirtualHost *:80>
    ServerName www.another-example.net
    DocumentRoot /var/www/another-example
</VirtualHost>
```

Aquí, `example1.com`, `example2.org`, y `another-example.net`. Son dominios distintos alojados en el mismo servidor. El servidor web utiliza el `Host` encabezado para ofrecer el contenido apropiado según el nombre de dominio solicitado.

### Búsqueda de host virtual del servidor
A continuación se ilustra el proceso de cómo un servidor web determina el contenido correcto a servir basándose en el `Host` encabezamiento:
![](https://mermaid.ink/svg/pako:eNqNUsFuwjAM_ZUop00CPqAHDhubuCBNBW2XXrzUtNFap3McOoT496WUVUA3aTkltp_f84sP2rgcdaI9fgYkgwsLBUOdkYqnARZrbAMk6oFd65HHiTd8XyPvfku9WpYA1dJ5eXS0tcW4ZOFMqJEkdU4y6vNnqul8PvRO1HKzeVFpp9KLumvbdmapAsItoy1KmRlX3_fwAXTd4OkLakuoOjVqiZAj_7_PaJJEPVvK1QrElJYK1UcDg1h3HmOEmV4LSlEC0-CA6i24Zb406IRhizuM7BV6BVFCit4FNuh77GX9DeGfmEu-s_mD4b5x5PH2Y4aqhfVNBftufomsGemJrpFrsHncqkOHy7SUWGOmk3jNgT8yndEx1kEQt96T0YlwwIlmF4pSJ1uofHyFJgf52cchirkVx6t-aU-7e_wG--_4bQ)

1. `Browser Requests a Website`: Cuando ingresa un nombre de dominio (por ejemplo, `www.inlanefreight.com`) en su navegador, inicia una solicitud HTTP al servidor web asociado con la dirección IP de ese dominio.
2. `Host Header Reveals the Domain`: El navegador incluye el nombre de dominio en la solicitud `Host` encabezado, que actúa como una etiqueta para informar al servidor web qué sitio web se está solicitando.
3. `Web Server Determines the Virtual Host`: El servidor web recibe la solicitud, examina el `Host` encabezado y consulta su configuración de host virtual para encontrar una entrada coincidente para el nombre de dominio solicitado.
4. `Serving the Right Content`: Al identificar la configuración de host virtual correcta, el servidor web recupera los archivos y recursos correspondientes asociados con ese sitio web desde su raíz de documentos y los envía de regreso al navegador como respuesta HTTP.

### Tipos de alojamiento virtual
`Name-Based Virtual Hosting`: Este método se basa únicamente en la `HTTP Host header`para distinguir entre sitios web. Es el método más común y flexible, ya que no requiere múltiples direcciones IP. Es rentable, fácil de configurar y es compatible con la mayoría de los servidores web modernos. Sin embargo, requiere que el servidor web admita archivos basados ​​en nombres. `virtual hosting` y puede tener limitaciones con ciertos protocolos como `SSL/TLS`.
`IP-Based Virtual Hosting`: Este tipo de hosting asigna una dirección IP única a cada sitio web alojado en el servidor. El servidor determina qué sitio web ofrecer en función de la dirección IP a la que se envió la solicitud. No depende del `Host header`, se puede utilizar con cualquier protocolo y ofrece un mejor aislamiento entre sitios web. Aún así, requiere múltiples direcciones IP, lo que puede resultar costoso y menos escalable.
`Port-Based Virtual Hosting`: Diferentes sitios web están asociados con diferentes puertos en la misma dirección IP. Por ejemplo, se puede acceder a un sitio web en el puerto 80, mientras que otro está en el puerto 8080. `Port-based virtual hosting` Se puede utilizar cuando las direcciones IP son limitadas, pero no es tan común ni tan fácil de usar como `name-based virtual hosting` y puede requerir que los usuarios especifiquen el número de puerto en la URL.

## Herramientas de descubrimiento de hosts virtuales

|Herramienta|Descripción|Características|
|---|---|---|
|[gobuster](https://github.com/OJ/gobuster)|Una herramienta multipropósito que se utiliza a menudo para la fuerza bruta de directorios/archivos, pero que también es eficaz para el descubrimiento de hosts virtuales.|Rápido, admite múltiples métodos HTTP y puede utilizar listas de palabras personalizadas.|
|[Feroxbuster](https://github.com/epi052/feroxbuster)|Similar a Gobuster, pero con una implementación basada en Rust, conocida por su velocidad y flexibilidad.|Admite recursividad, descubrimiento de comodines y varios filtros.|
|[ufff](https://github.com/ffuf/ffuf)|Otro fuzzer web rápido que se puede utilizar para el descubrimiento de hosts virtuales al difuminar el `Host` encabezamiento.|Opciones de filtrado y entrada de lista de palabras personalizables.|
### gobuster
```js
gobuster vhost -u http://<target_IP_address> -w <wordlist_file> --append-domain
```

