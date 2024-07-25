Imagina que eres un invitado a una gran fiesta en una casa. Si bien eres libre de socializar y explorar, es posible que haya ciertas salas marcadas como "Privadas" que se espera que evites. Esto es similar a como `robots.txt`funciones en el mundo del rastreo web. Actúa como un virtual " `etiquette guide`" para los bots, que describe a qué áreas de un sitio web se les permite acceder y cuáles están prohibidas.

## ¿Qué es robots.txt?
Técnicamente, `robots.txt` es un archivo de texto simple ubicado en el directorio raíz de un sitio web (por ejemplo, `www.example.com/robots.txt`). Cumple con el Estándar de exclusión de robots, pautas sobre cómo deben comportarse los rastreadores web cuando visitan un sitio web. Este archivo contiene instrucciones en forma de "directivas" que indican a los robots qué partes del sitio web pueden y no pueden rastrear.

### Comprender la estructura de robots.txt
El archivo robots.txt es un documento de texto sin formato que se encuentra en el directorio raíz de un sitio web. Sigue una estructura sencilla, con cada conjunto de instrucciones, o "registro", separados por una línea en blanco. Cada registro consta de dos componentes principales:
1. `User-agent`: esta línea especifica a qué rastreador o bot se aplican las siguientes reglas. Un comodín ( `*`) indica que las reglas se aplican a todos los bots. También se pueden seleccionar agentes de usuario específicos, como "Googlebot" (el rastreador de Google) o "Bingbot" (el rastreador de Microsoft).
2. `Directives`: Estas líneas proporcionan instrucciones específicas al usuario-agente identificado.

| Directiva     | Descripción                                                                                                                             | Ejemplo                                                                     |
| ------------- | --------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| `Disallow`    | Especifica rutas o patrones que el bot no debe rastrear.                                                                                | `Disallow: /admin/` (no permitir el acceso al directorio de administración) |
| `Allow`       | Permite explícitamente que el bot rastree rutas o patrones específicos, incluso si pertenecen a un ámbito más amplio. `Disallow` regla. | `Allow: /public/` (permitir el acceso al directorio público)                |
| `Crawl-delay` | Establece un retraso (en segundos) entre solicitudes sucesivas del bot para evitar sobrecargar el servidor.                             | `Crawl-delay: 10` (Retraso de 10 segundos entre solicitudes)                |
| `Sitemap`     | Proporciona la URL de un mapa del sitio XML para un rastreo más eficiente.                                                              | `Sitemap: https://www.example.com/sitemap.xml`                              |
### ¿Por qué respetar el archivo robots.txt?
Si bien el archivo robots.txt no se puede aplicar estrictamente (un robot malicioso aún podría ignorarlo), la mayoría de los rastreadores web y robots de motores de búsqueda legítimos respetarán sus directivas. Esto es importante por varias razones:
- `Avoiding Overburdening Servers`: Al limitar el acceso de los rastreadores a ciertas áreas, los propietarios de sitios web pueden evitar un tráfico excesivo que podría ralentizar o incluso bloquear sus servidores.
- `Protecting Sensitive Information`: Robots.txt puede proteger la información privada o confidencial para que no sea indexada por los motores de búsqueda.
- `Legal and Ethical Compliance`: En algunos casos, ignorar las directivas de robots.txt podría considerarse una violación de los términos de servicio de un sitio web o incluso una cuestión legal, especialmente si implica acceder a datos privados o protegidos por derechos de autor.

## robots.txt en reconocimiento web
Para el reconocimiento web, robots.txt constituye una valiosa fuente de inteligencia. Si bien respetan las directivas descritas en este archivo, los profesionales de la seguridad pueden obtener información crucial sobre la estructura y las posibles vulnerabilidades de un sitio web objetivo:
- `Uncovering Hidden Directories`: Las rutas no permitidas en robots.txt a menudo apuntan a directorios o archivos que el propietario del sitio web desea intencionalmente mantener fuera del alcance de los rastreadores de los motores de búsqueda. Estas áreas ocultas pueden albergar información confidencial, archivos de respaldo, paneles administrativos u otros recursos que podrían interesar a un atacante.
- `Mapping Website Structure`: Al analizar las rutas permitidas y no permitidas, los profesionales de seguridad pueden crear un mapa rudimentario de la estructura del sitio web. Esto puede revelar secciones que no están vinculadas desde la navegación principal, lo que podría conducir a páginas o funcionalidades no descubiertas.
- `Detecting Crawler Traps`: Algunos sitios web incluyen intencionalmente directorios "honeypot" en robots.txt para atraer robots maliciosos. La identificación de dichas trampas puede proporcionar información sobre la conciencia de seguridad y las medidas defensivas del objetivo.

```txt
User-agent: *
Disallow: /admin/
Disallow: /private/
Allow: /public/

User-agent: Googlebot
Crawl-delay: 10

Sitemap: https://www.example.com/sitemap.xml
```

Este archivo contiene las siguientes directivas:
- Todos los agentes de usuario no tienen permitido acceder a `/admin/` y `/private/` directorios.
- Todos los agentes de usuario pueden acceder al `/public/` directorio.
- El `Googlebot` (El rastreador web de Google) recibe instrucciones específicas de esperar 10 segundos entre solicitudes.
- El mapa del sitio, ubicado en `https://www.example.com/sitemap.xml`, se proporciona para facilitar el rastreo y la indexación.

