El rastreo web es vasto e intrincado, pero no es necesario que usted se embarque en este viaje solo. Hay una gran cantidad de herramientas de rastreo web disponibles para ayudarlo, cada una con sus propias fortalezas y especialidades. Estas herramientas automatizan el proceso de rastreo, haciéndolo más rápido y eficiente, permitiéndole concentrarse en analizar los datos extraídos.

## Rastreadores web populares
1. `Burp Suite Spider`: Burp Suite, una plataforma de prueba de aplicaciones web ampliamente utilizada, incluye un potente rastreador activo llamado Spider. Spider se destaca en el mapeo de aplicaciones web, la identificación de contenido oculto y el descubrimiento de vulnerabilidades potenciales.
2. `OWASP ZAP (Zed Attack Proxy)`: ZAP es un escáner de seguridad de aplicaciones web gratuito y de código abierto. Se puede utilizar en modo automatizado y manual e incluye un componente de araña para rastrear aplicaciones web e identificar posibles vulnerabilidades.
3. `Scrapy (Python Framework)`: Scrapy es un marco de Python versátil y escalable para crear rastreadores web personalizados. Proporciona funciones completas para extraer datos estructurados de sitios web, manejar escenarios de rastreo complejos y automatizar el procesamiento de datos. Su flexibilidad lo hace ideal para tareas de reconocimiento personalizadas.
4. `Apache Nutch (Scalable Crawler)`: Nutch es un rastreador web de código abierto altamente extensible y escalable escrito en Java. Está diseñado para manejar rastreos masivos en toda la web o centrarse en dominios específicos. Si bien requiere más experiencia técnica para instalarlo y configurarlo, su potencia y flexibilidad lo convierten en un activo valioso para proyectos de reconocimiento a gran escala.

### Scrapy

Antes de comenzar, asegúrese de tener Scrapy instalado en su sistema. Si no lo hace, puede instalarlo fácilmente usando pip, el instalador del paquete Python:
```js
pip3 install scrapy
```

### Reconspider
Primero, ejecute este comando en su terminal para descargar la araña scrapy personalizada, `ReconSpider`y extráigalo al directorio de trabajo actual.
```js
wget https://academy.hackthebox.com/storage/modules/279/ReconSpider.zip
unzip ReconSpider.zip 
```

```js
python3 ReconSpider.py http://inlanefreight.com
```

### results.json
Cada clave en el archivo JSON representa un tipo diferente de datos extraídos del sitio web de destino:

|Clave JSON|Descripción|
|---|---|
|`emails`|Enumera las direcciones de correo electrónico encontradas en el dominio.|
|`links`|Enumera las URL de los enlaces encontrados dentro del dominio.|
|`external_files`|Enumera las URL de archivos externos, como archivos PDF.|
|`js_files`|Enumera las URL de los archivos JavaScript utilizados por el sitio web.|
|`form_fields`|Enumera los campos de formulario que se encuentran en el dominio (vacíos en este ejemplo).|
|`images`|Enumera las URL de las imágenes encontradas en el dominio.|
|`videos`|Enumera las URL de los vídeos encontrados en el dominio (vacíos en este ejemplo).|
|`audio`|Enumera las URL de los archivos de audio encontrados en el dominio (vacíos en este ejemplo).|
|`comments`|Enumera los comentarios HTML que se encuentran en el código fuente.|