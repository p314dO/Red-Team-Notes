### Administración de memoria en procesos

La gestión de memoria de proceso es un aspecto fundamental de los sistemas operativos, responsable de organizar y manejar los recursos de memoria para los programas en ejecución (procesos).

Los sistemas operativos modernos implementan un concepto conocido como memoria virtual, que abstrae los recursos de memoria física y presenta a cada proceso un espacio de direcciones virtuales.

Cada proceso percibe su memoria como un bloque contiguo de direcciones de memoria, comenzando desde la dirección 0 y extendiéndose hasta el espacio direccionable máximo.

### Segmentación de memoria

La memoria de un proceso típicamente se divide en segmentos, cada uno con un propósito específico:
- **Segmento de Código**: Contiene el código ejecutable del programa.
- **Segmento de Datos**: Almacena datos inicializados, como variables globales y variables estáticas.
- **Segmento BSS**: Contiene datos no inicializados, inicializados a cero durante la ejecución del programa.
- **Segmento de Montón (Heap)**: Memoria asignada dinámicamente para estructuras de datos del programa (por ejemplo, memoria del montón asignada usando funciones como malloc() y free()).
- **Segmento de Pila (Stack)**: Almacena los marcos de llamadas a funciones, variables locales y parámetros de función. La pila crece y disminuye dinámicamente conforme se llaman y retornan funciones.
![[Pasted image 20240625132445.png]]


El segmento de texto, o segmento de instrucciones, está fijo por el programa y contiene el código del programa. Esta región se marca como solo lectura ya que el programa no debería cambiar durante la ejecución.

El segmento de datos se divide en datos inicializados y no inicializados. Los datos inicializados incluyen elementos como variables estáticas y globales declaradas que están predefinidas y pueden modificarse. Los datos no inicializados, denominados Block Started by Symbol (BSS), también inicializan variables que se inicializan a cero o no tienen inicialización explícita (por ejemplo, static int t).

A continuación está el montón (heap), que comienza justo después del segmento BSS. Durante la ejecución, el programa puede solicitar más espacio en memoria a través de llamadas al sistema como brk y sbrk, utilizadas por funciones como malloc(), realloc() y free().

Finalmente, está la pila (stack). Para nuestros propósitos, esta es la estructura más importante con la que trataremos.