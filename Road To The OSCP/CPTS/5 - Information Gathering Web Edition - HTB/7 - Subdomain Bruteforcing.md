`Subdomain Brute-Force Enumeration`es una poderosa técnica de descubrimiento de subdominios activos que aprovecha listas predefinidas de posibles nombres de subdominios. Este enfoque prueba sistemáticamente estos nombres con el dominio de destino para identificar subdominios válidos.

| Tool                                                    | Description                                                                                                                                                    |
| ------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [dnsenum](https://github.com/fwaeytens/dnsenum)         | Completa herramienta de enumeración de DNS que admite ataques de diccionario y de fuerza bruta para descubrir subdominios.                                     |
| [fierce](https://github.com/mschwager/fierce)           | Herramienta fácil de usar para el descubrimiento recursivo de subdominios, que incluye detección de comodines y una interfaz fácil de usar.                    |
| [dnsrecon](https://github.com/darkoperator/dnsrecon)    | Herramienta versátil que combina múltiples técnicas de reconocimiento de DNS y ofrece formatos de salida personalizables.                                      |
| [amass](https://github.com/owasp-amass/amass)           | Herramienta mantenida activamente centrada en el descubrimiento de subdominios, conocida por su integración con otras herramientas y amplias fuentes de datos. |
| [assetfinder](https://github.com/tomnomnom/assetfinder) | Herramienta sencilla pero eficaz para encontrar subdominios utilizando diversas técnicas, ideal para escaneos rápidos y ligeros.                               |
| [puredns](https://github.com/d3mondev/puredns)          | Potente y flexible herramienta de fuerza bruta DNS, capaz de resolver y filtrar resultados de forma efectiva.                                                  |
### DNSenum
```js
dnsenum --enum inlanefreight.com -f ~/SecLists/Discovery/DNS/subdomains-top1million-110000.txt -r
```

- `dnsenum --enum inlanefreight.com`: Especificamos el dominio de destino que queremos enumerar, junto con un acceso directo para algunas opciones de ajuste ``--enum`.
- `-f /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt`: Indicamos la ruta a la lista de palabras SecLists que usaremos para la fuerza bruta. Ajuste la ruta si su instalación de SecLists es diferente.
- `-r`: Esta opción habilita la fuerza bruta recursiva en subdominios, lo que significa que si `dnsenum` encuentra un subdominio, luego intentará enumerar los subdominios de ese subdominio.

