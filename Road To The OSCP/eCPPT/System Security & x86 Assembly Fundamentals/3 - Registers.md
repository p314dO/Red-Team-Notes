Los registros de la CPU son ubicaciones de almacenamiento pequeñas y de alta velocidad situadas dentro de la Unidad Central de Procesamiento (CPU).
Se utilizan para mantener temporalmente los datos que están siendo procesados o manipulados por la CPU.
Los registros juegan un papel crucial en la ejecución de instrucciones y en la gestión de datos dentro del sistema informático.

La arquitectura de la CPU (32 bits y 64 bits) se refiere al ancho/tamaño de los registros de la CPU.
Cada CPU tiene su conjunto fijo de registros a los que se accede cuando es necesario.
Puedes pensar en los registros como variables temporales utilizadas por la CPU para recuperar y almacenar datos.
Para este curso, nos centraremos en un grupo específico de registros: los Registros de Propósito General (GPRs).

### General Purpose Registers

Los registros de propósito general se utilizan para almacenar datos temporalmente durante la ejecución del programa.
Son versátiles y pueden contener diversos tipos de datos, como enteros, direcciones de memoria o resultados intermedios de operaciones aritméticas/lógicas.
Ejemplos de registros de propósito general incluyen:
- EAX, EBX, ECX, EDX: Usados para manipulación general de datos y operaciones aritméticas.
- ESI, EDI: A menudo utilizados para operaciones de manipulación de cadenas.
- ESP, EBP: Utilizados para gestionar la pila (ESP para el puntero de pila, EBP para el puntero base).
En arquitecturas de 64 bits (x64), los registros de propósito general se extienden a 64 bits (RAX, RBX, RCX, RDX, etc.), lo que proporciona un espacio de memoria direccionable mayor y soporte para tipos de datos más grandes.

La tabla en la siguiente diapositiva resume los ocho registros de propósito general.
Presta atención a la convención de nombres x86, ya que se refiere a los nombres de registros para la arquitectura x86 (32 bits).
Veremos cómo cambian los nombres para las arquitecturas de 64 bits, 32 bits, 16 bits y 8 bits a medida que avancemos en las diapositivas.

![[2024-06-24_06-26.png]]

### General Purpose Registers

EAX (Registro del Acumulador)
- El registro EAX es el registro principal del acumulador utilizado para operaciones aritméticas y lógicas. A menudo se usa para almacenar operandos y recibir resultados de cálculos. En ciertos contextos, almacena valores de retorno de funciones y se utiliza como registro temporal para cálculos intermedios.

EBX (Registro Base)
- El registro EBX, también conocido como registro base, típicamente se usa como puntero a datos en memoria o como dirección base para operaciones de memoria. También puede servir como registro de propósito general para almacenar datos temporalmente durante cálculos.

ECX (Registro de Contador)
- El registro ECX, conocido como registro de contador, se utiliza comúnmente para control de bucles y conteo de iteraciones. A menudo se utiliza junto con la instrucción LOOP para implementar bucles y tareas repetitivas.

EDX (Registro de Datos)
- El registro EDX, o registro de datos, se utiliza en conjunto con EAX para ciertas operaciones aritméticas que requieren un rango más amplio de almacenamiento de datos (por ejemplo, multiplicación y división de 64 bits). También puede servir como registro de propósito general para almacenar datos.

ESI (Registro de Índice de Origen)
- El registro ESI, conocido como registro de índice de origen, se utiliza comúnmente en operaciones de manipulación de cadenas. Típicamente contiene la dirección de inicio de los datos de origen o la cadena de origen durante operaciones como copiar, comparar o buscar cadenas.

EDI (Registro de Índice de Destino)
- El registro EDI, o registro de índice de destino, complementa al registro ESI en operaciones de manipulación de cadenas. Normalmente contiene la dirección de inicio de los datos de destino o la cadena de destino durante operaciones de cadena como copia o concatenación.

ESP (Registro de Puntero de Pila)
- El registro ESP, o registro de puntero de pila, apunta a la parte superior de la pila en memoria. Se utiliza para gestionar la pila, un área especial de memoria utilizada para almacenar parámetros de funciones, variables locales, direcciones de retorno y otros datos durante la ejecución del programa.

EBP (Registro de Puntero Base)
- El registro EBP, conocido como registro de puntero base, se utiliza comúnmente junto con el registro ESP para acceder a parámetros y variables locales dentro de llamadas a funciones. Sirve como punto de referencia para acceder a datos almacenados en la pila.

### Architecture Specific Register Names

Aunque esta información puede parecer abrumadora, todo cobrará sentido una vez que exploremos la Pila.
La convención de nombres de los antiguos CPU de 8 bits dividía los registros de 16 bits en dos partes:
- Un byte bajo, identificado con una "L" al final del nombre.
- Un byte alto, identificado con una "H" al final del nombre.

La convención de nombres de 16 bits combina la "L" y la "H", reemplazándolas con una "X". Mientras que para los registros de Puntero de Pila, Puntero Base, Índice de Origen y Índice de Destino, simplemente elimina la "L".

En la representación de 32 bits, el acrónimo/nombre del registro se prefija con una "E", que significa extendido. En cambio, en la representación de 64 bits, la "E" se reemplaza con una "R".

La tabla en la siguiente diapositiva detalla las convenciones de nombres para registros de 8 bits, 16 bits, 32 bits y 64 bits.

Aunque principalmente utilizaremos la convención de nombres de 32 bits a lo largo de este curso, también es útil entender la convención de nombres de 64 bits.

![[2024-06-24_06-32.png]]

### Instruction Pointer (EIP)

Además de los ocho registros de propósito general, hay otro registro importante para lo que haremos en este curso: el EIP (convención de nombres x86).
El Puntero de Instrucción (EIP) controla la ejecución del programa almacenando un puntero a la dirección de la siguiente instrucción (código máquina) que será ejecutada.

NOTA: Indica a la CPU dónde está la siguiente instrucción.