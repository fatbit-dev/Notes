# Tema 0 - Introducción y conceptos básicos

## Nociones básicas sobre ordenadores

Componentes básicos:

	- Placa base (y buses de datos)
	- Microprocesador
	- Memoria RAM
	- Disco Duro
	- Tarjeta gráfica
	- Puertos de E/S (USB, Ethernet, Thunderbolt, HDMI, DisplayPort, etc).
	- Periféricos (Ratón, Teclado, Monitor, Interfaces de red, Lector de huellas, Webcam, etc).

Todos son componentes electrónicos, que funcionan básicamente con ciertos voltajes (o ausencia de los mismos): 1's y 0's

### ¿Qué ocurre cuando ejecutamos un programa?

El sistema operativo se encarga de mover, desde el disco duro a la memoria RAM, tanto el código (compilado) como los datos del programa. Así el sistema operativo va planificando y ejecutando cada programa (en fin último, es la CPU quien ejecutará las operaciones que vaya planificando el sistema operativo - *scheduler*).

## ¿Qué es un Sistema Operativo?

Un SO está compuesto por muchos programas, que representan una abstracción sobre el hardware: nos permiten manejar el hardware de un ordenador sin necesidad de saber de electrónica.

Además, un SO nos permite ejecutar programas. Estos programas pueden ser creados por nosotros mismos, o por terceras partes.

En los sistemas operativos modernos, como los que usamos a menudo en nuestros PC, el programa con el que más interactuamos es con el entorno gráfico. Estamos acostumbrados a carpetas, ficheros, ventanas, unidades de disco, y en definitiva, herramientas y programas muy sofisticados.

Sin embargo, estos programas gráficos se sustentan en otros componentes de más bajo nivel, y que también forman parte del sistema operativo. Conocer el manejo de un sistema operativo a un nivel más profundo, nos permite **administrar** ese sistema.

Aunque en clase podemos usar entornos gráficos, nos vamos a centrar en el manejo del sistema operativo a nivel de administración.

### ¿Qué es Linux? ¿Qué es eso de GNU? ¿Qué es una distribución Linux?

### ¿En qué se diferencia un servidor de otros ordenadores?

## ¿Qué es una licencia de software?

Es básicamente un contrato social entre el creador del software y los usuarios del mismo.

El software, como producto, se rige según la legislación sobre ~~`Propiedad Intelectual`~~: `Derechos de autoría`, `Derechos de propiedad industrial` y `Derechos mercantiles`.

### Software propietario vs. Software libre

## Lenguajes de programación

Lenguajes compilados vs. Lenguajes interpretados.

Entonces, ¿qué es eso de la JVM, o Java Virtual Machine?

## Máquinas virtuales

A veces los servicios que se ejecutan en un servidor son sencillos, y no tienen mucha demanda de hardware, como capacidad de proceso (CPU), o demanda de memoria (RAM) o de almacenamiento (HD). La virtualización permite ejecutar simultáneamente varios sistemas operativos independientes sobre el mismo hardware. Es decir, que en un ordenador podemos ejecutar varias máquinas virtuales, cada una con su sistema operativo.
