Una de las piedras angulares de esta confianza es la `Secure Sockets Layer/Transport Layer Security` ( `SSL/TLS`), que cifra la comunicación entre su navegador y un sitio web. En el corazón de SSL/TLS se encuentra el `digital certificate`, un pequeño archivo que verifica la identidad de un sitio web y permite una comunicación segura y cifrada.

## ## Búsqueda de registros CT

Hay dos opciones populares para buscar registros de CT:

|Herramienta|Características clave|Casos de uso|Ventajas|Contras|
|---|---|---|---|---|
|[crt.sh](https://crt.sh/)|Interfaz web fácil de usar, búsqueda sencilla por dominio, muestra detalles del certificado, entradas SAN.|Búsquedas rápidas y sencillas, identificación de subdominios, consulta del historial de emisión de certificados.|Gratis, fácil de usar, no es necesario registrarse.|Opciones limitadas de filtrado y análisis.|
|[censys](https://search.censys.io/)|Potente buscador de dispositivos conectados a Internet, filtrado avanzado por dominio, IP, atributos de certificado.|Análisis en profundidad de certificados, identificación de configuraciones erróneas, búsqueda de certificados y hosts relacionados.|Amplias opciones de filtrado y datos, acceso API.|Requiere registro (nivel gratuito disponible).|
### búsqueda crt.sh
```js
curl -s "https://crt.sh/?q=facebook.com&output=json" | jq -r '.[]
 | select(.name_value | contains("dev")) | .name_value' | sort -u
```

- `curl -s "https://crt.sh/?q=facebook.com&output=json"`: este comando recupera la salida JSON de crt.sh para los certificados que coinciden con el dominio `facebook.com`.
- `jq -r '.[] | select(.name_value | contains("dev")) | .name_value'`: Esta parte filtra los resultados JSON, seleccionando solo las entradas donde `name_value` El campo (que contiene el dominio o subdominio) incluye la cadena " `dev.`" El `-r` la bandera dice `jq` para generar cadenas sin formato.
- `sort -u`: Esto ordena los resultados alfabéticamente y elimina duplicados.
