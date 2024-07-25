La Unidad Central de Procesamiento (CPU) es comúnmente referida como el cerebro de una computadora, responsable de ejecutar instrucciones y realizar cálculos.
La Unidad Central de Procesamiento (CPU) es el dispositivo encargado de ejecutar el código máquina de un programa.
El código máquina, o lenguaje máquina, es el conjunto de instrucciones que la CPU procesa.

Cada instrucción es un comando primitivo que realiza una operación específica como mover datos entre registros, trabajar con memoria, cambiar el flujo de ejecución de un programa, y realizar operaciones aritméticas y más.
Como regla general, cada CPU tiene su propia Arquitectura de Conjunto de Instrucciones (ISA).
Las instrucciones de la CPU se representan en formato hexadecimal (HEX). Debido a su naturaleza inherentemente ilegible y compleja, es imposible para los humanos leerla o utilizarla en su formato natural.

Como resultado, el código máquina se traduce al Lenguaje de Ensamblador (ASM).
El lenguaje de ensamblador (ASM) es código mnemotécnico (un lenguaje más legible) que los humanos pueden entender e interpretar.
El lenguaje de ensamblador es un lenguaje de programación de bajo nivel que está estrechamente relacionado con las instrucciones de código máquina de una arquitectura de CPU específica.
Proporciona una representación simbólica de las instrucciones de máquina y permite a los programadores escribir instrucciones utilizando mnemónicos y etiquetas simbólicas en lugar de código binario.

El lenguaje de ensamblador está estrechamente ligado a la arquitectura de la CPU objetivo.
Diferentes arquitecturas de CPU tienen sus propios conjuntos de instrucciones y lenguajes de ensamblador.
Por ejemplo, el lenguaje de ensamblador x86 se utiliza para procesadores Intel y AMD, mientras que el lenguaje de ensamblador ARM se utiliza para procesadores basados en ARM.

![[2024-06-24_05-04.png]]


### Unidad de Control (CU)

- La Unidad de Control es responsable de coordinar y controlar las operaciones de la CPU.
- Obtiene instrucciones de la memoria, las decodifica y gestiona la ejecución de instrucciones dirigiendo el movimiento de datos y el flujo de control dentro de la CPU.

### Unidad Aritmético Lógica (ALU)
- La Unidad Aritmético Lógica es el componente responsable de realizar operaciones aritméticas y lógicas.
- Puede realizar operaciones básicas como suma, resta, multiplicación, división, así como operaciones lógicas como Y (AND), O (OR) y NO (NOT).

### Registros
- Los registros son ubicaciones de almacenamiento pequeñas y de alta velocidad dentro de la CPU utilizadas para almacenar datos temporalmente durante el procesamiento. Algunos tipos comunes de registros incluyen:
	- Contador de Programa (PC): Contiene la dirección de memoria de la próxima instrucción a ser obtenida.
	- Registro de Instrucción (IR): Contiene la instrucción actualmente en ejecución.
	 - Acumulador: Almacena el resultado de operaciones aritméticas y lógicas.
	- Registros de Propósito General: Utilizados para almacenar valores intermedios y operandos durante la ejecución de instrucciones.
	- Arquitectura de Conjunto de Instrucciones (ISA) de la CPU

### CPU Instruction Set Architecture (ISA)
- Cada CPU tiene su propia arquitectura de conjunto de instrucciones (ISA). La ISA es el conjunto de instrucciones que un programador (o un compilador) debe entender y usar para escribir un programa correctamente para esa CPU y máquina específica.
- En otras palabras, la ISA es lo que un programador puede ver: memoria, registros, instrucciones, etc. Proporciona toda la información necesaria para quien desea escribir un programa en ese lenguaje máquina.
- La ISA más común es el conjunto de instrucciones x86 (o arquitectura) que se originó a partir del procesador Intel 8086.
- El acrónimo x86 identifica a los procesadores de 32 bits, mientras que x64 (también conocido como x86_64 o AMD64) identifica las versiones de 64 bits.

