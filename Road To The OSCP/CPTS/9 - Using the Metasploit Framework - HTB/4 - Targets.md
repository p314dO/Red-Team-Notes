## Seleccionar un objetivo

 ¿Qué pasa si cambiamos el módulo de explotación por algo que necesite rangos objetivo más específicos? El siguiente exploit está dirigido a:

- `MS12-063 Microsoft Internet Explorer execCommand Use-After-Free Vulnerability`.
Si queremos saber más sobre este módulo específico y qué hace la vulnerabilidad detrás de él, podemos usar el `info`dominio. Este comando puede ayudarnos cuando no estemos seguros del origen o funcionalidad de diferentes exploits o módulos auxiliares. Teniendo en cuenta que siempre se considera una mejor práctica auditar nuestro código para detectar cualquier generación de artefactos o "características adicionales", el `info`El comando debería ser uno de los primeros pasos que damos cuando utilizamos un nuevo módulo. De esta manera, podemos familiarizarnos con la funcionalidad del exploit y al mismo tiempo garantizar un entorno de trabajo limpio y seguro tanto para nosotros como para nuestros clientes.

#### MSF - Selección de objetivos
```js
msf6 exploit(windows/browser/ie_execcommand_uaf) > info
```

```js
msf6 exploit(windows/browser/ie_execcommand_uaf) > show targets

Exploit targets:

   Id  Name
   --  ----
   0   Automatic
   1   IE 7 on Windows XP SP3
   2   IE 8 on Windows XP SP3
   3   IE 7 on Windows Vista
   4   IE 8 on Windows Vista
   5   IE 8 on Windows 7
   6   IE 9 on Windows 7


msf6 exploit(windows/browser/ie_execcommand_uaf) > set target 6

target => 6
```

