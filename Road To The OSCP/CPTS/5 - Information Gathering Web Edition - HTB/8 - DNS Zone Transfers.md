Este mecanismo, diseñado para replicar registros DNS entre servidores de nombres, puede convertirse sin darse cuenta en una mina de oro de información para miradas indiscretas si se configura mal. Una transferencia de zona DNS es esencialmente una copia total de todos los registros DNS dentro de una zona (un dominio y sus subdominios) de un servidor de nombres a otro. Este proceso es esencial para mantener la coherencia y la redundancia entre los servidores DNS. Sin embargo, si no se protege adecuadamente, partes no autorizadas pueden descargar el archivo de zona completo, revelando una lista completa de subdominios, sus direcciones IP asociadas y otros datos DNS confidenciales.

![](https://mermaid.ink/svg/pako:eNqNkc9qwzAMxl9F-JSx7gV8KISWXcY2aHYYwxdjK39obGWKvBFK333ukg5aGNQnW9b3Q_q-g3LkUWk14mfC6HDb2YZtMBHyGdFR9JanCvkL-WG9vh-4C38FDeX74w52J-0oUHxQRHhjG8ca-W5mXAgy4YqpoXotM8EReygqsSxANZRJWuJOpoXSEw0gC3ku3QTfvlQLfBZh9DeOdbELbCgMPQr-58u1LZsnKEq3j_Tdo28wYJS8iVqpgBxs57PjhxPLKGnzr1E6XzNxb5SJx9xnk1A1Rae0cMKVYkpNq3Rt-zG_0uCtnLM6t6DvhPh5zvM31uMPG8qm-A)

1. `Zone Transfer Request (AXFR)`: El servidor DNS secundario inicia el proceso enviando una solicitud de transferencia de zona al servidor primario. Esta solicitud normalmente utiliza el tipo AXFR (Transferencia de zona completa).
2. `SOA Record Transfer`: Al recibir la solicitud (y potencialmente autenticar el servidor secundario), el servidor primario responde enviando su registro de Inicio de autoridad (SOA). El registro SOA contiene información vital sobre la zona, incluido su número de serie, lo que ayuda al servidor secundario a determinar si los datos de su zona están actualizados.
3. `DNS Records Transmission`: Luego, el servidor primario transfiere todos los registros DNS de la zona al servidor secundario, uno por uno. Esto incluye registros como A, AAAA, MX, CNAME, NS y otros que definen los subdominios, servidores de correo, servidores de nombres y otras configuraciones del dominio.
4. `Zone Transfer Complete`: Una vez que se han transmitido todos los registros, el servidor primario señala el final de la transferencia de zona. Esta notificación informa al servidor secundario que ha recibido una copia completa de los datos de la zona.
5. `Acknowledgement (ACK)`: El servidor secundario envía un mensaje de confirmación al servidor primario, confirmando la recepción y el procesamiento exitosos de los datos de la zona. Esto completa el proceso de transferencia de zona.

#### Transferencias de zona de explotación
Puedes usar el `dig` comando para solicitar una transferencia de zona:
```js
dig axfr @nsztm1.digi.ninja zonetransfer.me
```

Este comando instruye `dig` para solicitar una transferencia de zona completa ( `axfr`) del servidor DNS responsable de `zonetransfer.me`. Si el servidor está mal configurado y permite la transferencia, recibirá una lista completa de los registros DNS para el dominio, incluidos todos los subdominios.

