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

1. top - 03:10:18 up  7:52,  3 users,  load average: 0,01, 0,27, 0,42
2. Tasks: 141 total,   3 running,  89 sleeping,   0 stopped,   0 zombie
3. %Cpu(s):  0,3 us,  0,0 sy,  0,0 ni, 99,7 id,  0,0 wa,  0,0 hi,  0,0 si,  0,0 st
4. KiB Mem :  2038132 total,   339236 free,  1340556 used,   358340 buff/cache
5. KiB Swap:   839676 total,   555004 free,   284672 used.   518248 avail Mem

6. PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
7. 2980 fabi      20   0 2079052 123280  52368 S  0,7  6,0   2:24.41 Web Content
8.  2369 root      20   0  485972  76288  13420 S  0,3  3,7  11:53.41 X
9.  2730 fabi      20   0 8778044 277452  77788 S  0,3 13,6  10:47.08 firefox
10. 7884 fabi      20   0  162020   4560   3892 R  0,3  0,2   0:00.36 top


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

    Line 2 Tasks is just another name for processes. It's typical to have quite a few processes running on your system at any given time. Most of them will be system processes. Many of them will typically be sleeping. This is ok. It just means they are waiting until a particular event occurs, which they will then act upon.
    Line 3 This is a breakdown of working memory (RAM). Don't worry if a large amount of your memory is used. Linux keeps recently used programs in memory to speed up performance if they are run again. If another process needs that memory, they can easily be cleared to accommodate this.
    Line 4 This is a breakdown of Virtual memory on your system. If a large amount of this is in use, you may want to consider increasing it's size. For most people with most modern systems having gigabytes of RAM you shouldn't experience any issues here.
    Lines 6 - 10 Finally is a listing of the most resource intensive processes on the system (in order of resource usage). This list will update in real time and so is interesting to watch to get an idea of what is happening on your system. The two important columns to consider are memory and CPU usage. If either of these is high for a particular process over a period of time, it may be worth looking into why this is so.The USER column shows who owns the process and the PID column identifies a process's Process ID which is a unique identifier for that process.





## Para ampliar

Este

## Algunos recursos útiles

- [Permissions - Ryans Tutorials](https://ryanstutorials.net/linuxtutorial/permissions.php)
- [Permissions - IBM Developer](https://developer.ibm.com/tutorials/l-lpic1-104-5/)