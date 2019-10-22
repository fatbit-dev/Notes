# Tema 4 - Gestión de procesos (I)

Este tema trata sobre la gestión de procesos en ejecución.

Cuando se ejecuta un programa en Linux, se crea al menos un proceso con todo
el contexto de ejecución para ese programa. Normalmente los sistemas Linux son
muy estables, pero alguna vez ocurrirá algo inesperado, un programa se quedará
colgado, o querremos hacer algún ajuste: en ese momento habrá que lidiar con
procesos.

Un proceso puede ejecutarse en primer plano (*foreground*) o en segundo plano
(*background*). Hay procesos que están diseñados para ejecutarse en *background*,
y se les suele llamar **demonios**.

## top

Para ver la tabla de procesos, se usa el comando `top` (*table of processes*).
*top* nos proporciona un vistazo de tiempo real de todos los procesos que se 
ejecutan en el sistema.

```bash
top

# El resultado será algo similar a esto (hemos numerado las líneas):

# 1.  top - 03:10:18 up  7:52,  3 users,  load average: 0,01, 0,27, 0,42
# 2.  Tasks: 141 total,   3 running,  89 sleeping,   0 stopped,   0 zombie
# 3.  %Cpu(s):  0,3 us,  0,0 sy,  0,0 ni, 99,7 id,  0,0 wa,  0,0 hi,  0,0 si,  0,0 st
# 4.  KiB Mem :  2038132 total,   339236 free,  1340556 used,   358340 buff/cache
# 5.  KiB Swap:   839676 total,   555004 free,   284672 used.   518248 avail Mem
# 
# 6.  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
# 7.  2980 fabi      20   0 2079052 123280  52368 S  0,7  6,0   2:24.41 Web Content
# 8.  2369 root      20   0  485972  76288  13420 S  0,3  3,7  11:53.41 X
# 9.  2730 fabi      20   0 8778044 277452  77788 S  0,3 13,6  10:47.08 firefox
# 10. 7884 fabi      20   0  162020   4560   3892 R  0,3  0,2   0:00.36 top


man top
```
Veamos qué información ofrece *top*:

1. La primera línea nos informa de:
  - Hora del sistema.
  - *Uptime*, o cuánto lleva encendida la máquina.
  - Número de usuarios que han accedido al sistema.
  - Media de la carga de la CPU:
    - Carga media durante el último minuto.
    - Carga media durante los últimos 5 minutos.
    - Carga media durante los últimos 15 minutos.

2. La segunda línea muestra el número de procesos en ejecución y su estado. La mayoría serán procesos del sistema, que normalmente estarán en estado durmiente (*sleeping*). Esto quiere decir que son procesos que están esperando algún evento ante el cual reaccionar.

3. La tercera línea ofrece información sobre la CPU (en porcentajes):
  1. CPU usada por procesos del usuario.
  2. CPU usada por procesos del sistema.
  3. CPU usada por procesos que tienen un _**nice**ness_ definido.
  4. CPU libre.
  5. CPU esperando por alguna operación de E/S.
  6. CPU usada por interrupiones hardware.
  7. CPU usada por interrupiones software.
  8. *Steal time* (CPUs virtuales)

4. La cuarta línea ofrece un resumen sobre la RAM.

5. La quinta línea ofrece un resumen sobre la memoria virtual del sistema.

6. Las líneas 6 a 10 muestran los procesos que consumen más recursos. Esta información se actualiza constantemente, y ofrece una idea de qué procesos cargan el sistema. Normalmente se vigilan la memoria RAM y la CPU que están consumiendo los procesos. También aparece el usuario que ha ejecutado el proceso, y un *PID*, o identificador de proceso.

  1. *PID* del proceso.
  2. Usuario que ejecuta el proceso.
  3. Prioridad.
  4. Valor de *nice*.
  5. Memoria virtual usada por el proceso (*swap*).
  6. Memoria física usada por el proceso.
  7. Memoria compartida usada por el proceso.
  8. Estado actual de los procesos en ejecución.
  9. % CPU usada por el proceso.
  10. % RAM usada por el proceso.
  11. Tiempo que lleva el proceso en ejecución.
  12. Nombre del proceso.

Se pueden ordenar los resultados de *top* pulsando *`Shift + F`*. 

## ps

El comando `ps` muestra toda la información sobre los procesos en ejecución.

Es habitual usarlo junto a *grep* para obtener información sobre un proceso en particular.

```bash
ps aux

ps faux

ps aux | grep cron


man ps
```

## kill y killall

El comando `kill` sirve para enviar señales (*signals*) a un proceso (*PID*). Por ejemplo, la señales para eliminar procesos son muy habituales. La siguiente tabla muestra algunas de las señales más comunes:


| Nombre    | Número   | Descripción   |
| :-------- | :------: | :-----------: |
|  SIGHUP   |  1       |  Notifica a un proceso cuyo padre ha terminado su ejecución (muchas veces, el padre es un terminal |
|  SIGINT   |  2       |  Notifica a un proceso cuando el usuario envía una señal de interrupción (Ctrl + C) |
|  SIGQUIT  |  3       |  Notifica a un proceso cuando el usuario envía una señal de terminación (Ctrl + D) |
|  SIGFPE   |  8       |  Notifica a un proceso cuando se intenta realizar una operación matemática ilegal |
|  SIGKILL  |  9       |  Si un proceso recibe esta señal, debe terminar su ejecución inmediatamente, sin hacer ninguna limpieza |
|  SIGALRM  |  14      |  Señal de alarma de reloj (usada con temporizadores) |
|  SIGTERM  |  15      |  Señal de terminación de software (es la señal que kill envía por defecto) |
 	 		

```bash
# Envía la señal 1 (SIGTERM) a cron (ordena a cron que se cierre ordenadamente)
kill 1568

# Envía la señal 9 (SIGKILL) a cron (fuerza el cierre de cron)
kill -9 1568


man kill
```

También se pueden enviar señales por el nombre del programa (sin conocer su *PID*).

```bash
killall cron

killall -9 cron


man killall
```

## Otras terminales (Ctrl + Alt + Fx)

A veces un proceso puede dejar congelado un terminal. Podemos entonces acceder al sistema mediante otro terminal, para poder finalizar los procesos que estén congelados.

Para ello pulsamos la combinación de teclas `Ctrl + Alt + Fx`, por ejemplo _**Ctrl + Alt + F2**_.

## Procesos que se ejecutan en primer plano y en segundo plano

Normalmente los comandos que se introducen en el terminal sirven para ejecutar programas en `primer plano` (*foreground*). Cuando un programa se ejecuta en primer plano, su salida se visualiza por el terminal (que normalmente lo muestra por pantalla). El programa nos devuelve el control cuando se termina su ejecución, y volvemos a ver el *prompt*.

No obstante, hay comandos que se ejecutan en segundo plano (*background*), y que no suelen mostrar su salida por el terminal. Por ejemplo, muchos procesos del sistema se ejecuten en segundo plano, y su salida se envía hacia algún fichero de *log* (bitácora).

En cualquier caso, se pueden ejecutar comandos en primer y segundo plano:

```bash




```

### Matar un proceso en primer plano (Ctrl + C)

Antes se ha visto cómo se puede usar el comando *kill* para terminar un proceso. Si ese proceso se ha lanzado mediante un comando en la terminal, puede ser detenido pulsando `Ctrl + C`. Esto enviará la señal *SIGTERM* al programa en cuestión.

### fg

### bg

### Ctrl + Z

## Ejecutar varios procesos (&&, ||, ;)

### Operador ;

Para ejecutar un comando tras otro se usa el separador de comandos `;`.

```bash
echo "Emepzamos..." ; sleep 5 ; echo "...han pasado 5 segundos"
```

En el ejemplo anterior, se ejecutarán todos los comandos (dos *echo* y un *sleep*) uno tras otro y en orden.

### Operador &&

Sirve para ejecutar un comando y, si se ejecutó SIN errores, entonces se ejecuta el segundo comando.

```bash
cd
pwd
echo "Emepzamos..." && sleep 5 && echo "...han pasado 5 segundos"

mkdir /etc/nosepuede && cd /etc/nosepuede && echo "Estoy en $(pwd)"
pwd

mkdir ~/sisepuede && cd ~/sisepuede && echo "Estoy en $(pwd)"
pwd

cd
rmdir ~/sisepuede
```

### Operador ||

Sirve para ejecutar un comando y, si se ejecutó CON errores, entonces se ejecuta el segundo comando.

```bash
cd
pwd
echo "Emepzamos..." || sleep 5 || echo "...han pasado 5 segundos"

mkdir /etc/nosepuede || echo "No se ha podido crear /etc/nosepuede :("
pwd

mkdir ~/sisepuede || echo "No se ha podido crear ~/sisepuede..."
pwd
```

## Para ampliar

Este

## Algunos recursos útiles

- []()
- []()
