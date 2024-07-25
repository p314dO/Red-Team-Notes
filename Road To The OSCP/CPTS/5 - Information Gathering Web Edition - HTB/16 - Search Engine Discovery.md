## Operadores de búsqueda
|Operador|Descripción del operador|Ejemplo|Descripción de ejemplo|
|:--|:--|:--|:--|
|`site:`|Limita los resultados a un sitio web o dominio específico.|`site:example.com`|Encuentre todas las páginas de acceso público en example.com.|
|`inurl:`|Encuentra páginas con un término específico en la URL.|`inurl:login`|Busque páginas de inicio de sesión en cualquier sitio web.|
|`filetype:`|Busca archivos de un tipo particular.|`filetype:pdf`|Encuentre documentos PDF descargables.|
|`intitle:`|Busca páginas con un término específico en el título.|`intitle:"confidential report"`|Busque documentos titulados "informe confidencial" o variaciones similares.|
|`intext:` o `inbody:`|Busca un término dentro del cuerpo del texto de las páginas.|`intext:"password reset"`|Identifique páginas web que contengan el término "restablecimiento de contraseña".|
|`cache:`|Muestra la versión en caché de una página web (si está disponible).|`cache:example.com`|Vea la versión en caché de example.com para ver su contenido anterior.|
|`link:`|Encuentra páginas que enlazan a una página web específica.|`link:example.com`|Identifique los sitios web que enlazan con example.com.|
|`related:`|Encuentra sitios web relacionados con una página web específica.|`related:example.com`|Descubra sitios web similares a example.com.|
|`info:`|Proporciona un resumen de información sobre una página web.|`info:example.com`|Obtenga detalles básicos sobre example.com, como su título y descripción.|
|`define:`|Proporciona definiciones de una palabra o frase.|`define:phishing`|Obtenga una definición de "phishing" de varias fuentes.|
|`numrange:`|Busca números dentro de un rango específico.|`site:example.com numrange:1000-2000`|Busque páginas en example.com que contengan números entre 1000 y 2000.|
|`allintext:`|Busca páginas que contengan todas las palabras especificadas en el cuerpo del texto.|`allintext:admin password reset`|Busque páginas que contengan "admin" y "restablecer contraseña" en el texto del cuerpo.|
|`allinurl:`|Busca páginas que contengan todas las palabras especificadas en la URL.|`allinurl:admin panel`|Busque páginas con "admin" y "panel" en la URL.|
|`allintitle:`|Busca páginas que contengan todas las palabras especificadas en el título.|`allintitle:confidential report 2023`|Busque páginas que tengan "confidencial", "informe" y "2023" en el título.|
|`AND`|Limita los resultados al exigir que todos los términos estén presentes.|`site:example.com AND (inurl:admin OR inurl:login)`|Encuentre páginas de administración o inicio de sesión específicamente en example.com.|
|`OR`|Amplia los resultados incluyendo páginas con cualquiera de los términos.|`"linux" OR "ubuntu" OR "debian"`|Busque páginas web que mencionen Linux, Ubuntu o Debian.|
|`NOT`|Excluye los resultados que contienen el término especificado.|`site:bank.com NOT inurl:login`|Busque páginas en bank.com, excluidas las páginas de inicio de sesión.|
|`*` (comodín)|Representa cualquier carácter o palabra.|`site:socialnetwork.com filetype:pdf user* manual`|Busque manuales de usuario (guía de usuario, manual de usuario) en formato PDF en socialnetwork.com.|
|`..` (búsqueda de rango)|Encuentra resultados dentro de un rango numérico específico.|`site:ecommerce.com "price" 100..500`|Busque productos con precios entre 100 y 500 en un sitio web de comercio electrónico.|
|`" "` (comillas)|Busca frases exactas.|`"information security policy"`|Busque documentos que mencionen la frase exacta "política de seguridad de la información".|
|`-` (signo menos)|Excluye términos de los resultados de búsqueda.|`site:news.com -inurl:sports`|Busque artículos de noticias en news.com, excluyendo contenido relacionado con deportes.|
### Google Dorking
- Encontrar páginas de inicio de sesión:
    - `site:example.com inurl:login`
    - `site:example.com (inurl:login OR inurl:admin)`
- Identificación de archivos expuestos:
    - `site:example.com filetype:pdf`
    - `site:example.com (filetype:xls OR filetype:docx)`
- Descubriendo archivos de configuración:
    - `site:example.com inurl:config.php`
    - `site:example.com (ext:conf OR ext:cnf)` (busca extensiones comúnmente utilizadas para archivos de configuración)
- Localización de copias de seguridad de bases de datos:
    - `site:example.com inurl:backup`
    - `site:example.com filetype:sql`
