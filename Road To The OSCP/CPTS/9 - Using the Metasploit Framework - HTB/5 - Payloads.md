Un Payload en Metasploit se refiere a un módulo que ayuda al módulo exploit en (típicamente) devolver un shell al atacante. Las cargas útiles se envían junto con el exploit en sí para eludir los procedimientos de funcionamiento estándar del servicio vulnerable (trabajo del exploit) y luego se ejecutan en el sistema operativo de destino para devolver típicamente una conexión inversa al atacante y establecer un punto de apoyo (trabajo de la carga útil).

Por ejemplo, `windows/shell_bind_tcp` es una carga útil única sin etapa, mientras que `windows/shell/bind_tcp` consta de un escenario ( `bind_tcp`) y un escenario ( `shell`).

#### Meterpreter Payload

El `Meterpreter` La carga útil es un tipo específico de carga útil multifacética que utiliza `DLL injection`para garantizar que la conexión con el host de la víctima sea estable, difícil de detectar mediante comprobaciones simples y persistente tras reinicios o cambios en el sistema. Meterpreter reside completamente en la memoria del host remoto y no deja rastros en el disco duro, lo que hace que sea muy difícil de detectar con técnicas forenses convencionales. Además, se pueden crear scripts y complementos. `loaded and unloaded` dinámicamente según sea necesario.

## Buscando cargas útiles
Para seleccionar nuestra primera carga útil, necesitamos saber qué queremos hacer en la máquina de destino. Por ejemplo, si optamos por la persistencia del acceso, probablemente querremos seleccionar una carga útil de Meterpreter.

#### MSF: búsqueda de carga útil específica
```js
grep meterpreter show payloads
```

Explota el servicio Apache Druid y busca el archivo flag.txt. Envíe el contenido de este archivo como respuesta.

