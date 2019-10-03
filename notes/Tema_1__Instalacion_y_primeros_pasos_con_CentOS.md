# Tema 1 - Instalación y primeros pasos con CentOS

Vamos a instalar [CentOS 7](https://www.centos.org) en una máquina virtual. El software de virtualización que vamos a usar es [VirtualBox](https://www.virtualbox.com).

Nos vamos a centrar en el uso del CentOS sin entorno gráfico. Esto es porque normalmente los servidores son máquinas que no disponen de pantallas, y que suelen disponer de pocos perfiéricos.

Por tanto, vamos a instalar una versión mínima de CentOS 7 para arquitecturas de 64bit, que podemos descargar desde [este enlace](http://linuxmirror.es/centos/7.6.1810/isos/x86_64/CentOS-7-x86_64-Minimal-1810.iso).

Realizaremos el proceso de instalación en clase.

Cuando la instalación termine, accederemos al sistema mediante nuestras credenciales de acceso (usuario y contraseña). Si las credenciales son correctas, el sistema nos proporcionará una *`shell`*, y esta *shell* nos permite ejecutar comandos (acciones), y manejar el sistema operativo. 

Lo primero que nos muestra la *shell* es un `prompt`* desde el cual podemos ejecutar nuestros comandos. Un prompt puede tener un aspecto como este:

`fabi@localhost ~ $ `
  
    - *fabi*: es el nombre del usuario
    - *localhost*: es el nombre de la máquina.
    - *~* : es el directorio de trabajo, es decir, el directorio en el que nos encontramos.
    - *$*: es el final del prompt. A partir de aquí escribiremos nuestros comandos. Para el usuario `root`, el símbolo es `#` en lugar de $.

Si queremos terminar nuestra sesión ejecutamos:

```bash
exit
```

## Ficheros y directorios

### Nuestros primeros comandos

Veamos cómo ver el contenido de un directorio:

```bash
ls

# Y para ver ficheros ocultos:
ls -a
```
Veamos otro ejemplo:

```bash
ls -l /home/fabi

total 24
drwx------@   3 fabi  staff    96B  3 jul  2018 Applications/
lrwxr-xr-x    1 fabi  staff    14B 24 jul  2018 Bits@ -> /Volumes/Bits/
drwxr-xr-x    4 fabi  staff   128B 19 ago 15:31 Boostnote/
drwx------+  18 fabi  staff   576B  3 sep 19:01 Desktop/
drwx------+  39 fabi  staff   1,2K  9 ago 21:49 Documents/
drwx------+ 139 fabi  staff   4,3K 10 sep 21:16 Downloads/
drwxr-xr-x    6 fabi  staff   192B 11 jun 11:08 GISS/
drwx------@  66 fabi  staff   2,1K 25 oct  2018 Library/
drwx------+  12 fabi  staff   384B 17 ago 20:32 Movies/
drwx------+   5 fabi  staff   160B 17 ago  2018 Music/
drwx------+  41 fabi  staff   1,3K 18 ago 23:29 Pictures/
drwxr-xr-x+   4 fabi  staff   128B  2 jul  2018 Public/
drwx------    8 fabi  staff   256B 17 ago 20:38 VirtualBox VMs/
drwxr-xr-x@   6 fabi  staff   192B 14 oct  2018 devtools/
-rw-r--r--    1 fabi  staff     0B 11 sep 22:52 esto_es_muy_dificil__vamos_a_la_cafeteria.txt
-rw-r--r--    1 fabi  staff   534B  7 jul  2018 lelerele
-rw-r--r--    1 fabi  staff   103B  7 jul  2018 lolailo
-rw-r--r--    1 fabi  staff    14B 14 oct  2018 p.sh
drwxr-xr-x    4 fabi  staff   128B 12 nov  2018 project/
drwxr-xr-x    3 fabi  staff    96B 12 nov  2018 target/
lrwxr-xr-x    1 fabi  staff    24B  9 oct  2018 workspace@ -> /Volumes/Bits/workspace/

___________ ___ ____  _____   ____ ____________ ______________
     |       |    |     |       |       |              |
	 |       |    |     |       |       |              -> Nombre
	 |       |    |     |       |       |     
	 |       |    |     |       |       -> Fecha de modificación
	 |       |    |     |       |   
	 |       |    |     |       -> Tamaño en bytes (aunque arriba se ha usado la opción -h)
	 |       |    |     |
	 |       |    |     -> Grupo con permisos sobre el fichero
	 |       |    |
	 |       |    -> Usuario propietario del fichero
	 |       |
	 |       -> Número de enlaces
	 |
	 -> Permisos del fichero:
	   - Tipo de fichero (regular, directorio, enlace, etc).
	   - Permisos del propietario ( r w x )
	   - Permisos del grupo ( r - x )
	   - Permisos para el resto de usuarios ( r - - )

```

- Comandos y argumentos
- Opciones
- Salida

En general, las opciones pueden escribirse de dos formas: una forma larga, y una forma abreviada. Por ejemplo, estos dos comandos hacen lo mismo:

```bash
ls -a

ls --all

ls -R

ls -l
```

Usando la notación abreviada, podemos agrupar varias opciones de forma cómoda:

```bash
ls -laR

ls -lah
```

Veamos otro comando, un tento peculiar:

```bash
echo $PATH

/home/fabi/Library/Python/3.7/bin:/usr/local/bin:/Library/Frameworks/Python.framework/Versions/3.7/bin:/usr/local/apache-maven-3.3.9/bin:/home/fabi/dev/SDKs/android-sdk-macosx/platform-tools:/home/fabi/dev/SDKs/android-sdk-macosx/tools:/Library/Java/JavaVirtualMachines/jdk1.8.0_172.jdk/Contents/Home/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Applications/Wireshark.app/Contents/MacOS:/opt/X11/bin:/home/fabi/bin:/Applications/apache-maven-3.6.0/bin:/opt/scala-2.12.7/bin:/opt/sbt-1.2.6/bin
```

Mmm, probemos de nuevo:

```bash
echo $SHELL

/bin/bash
```

Acabamos de ver el contenido de unas `variables de entorno`.

Veamos un comando muy útil, que nos muestra el historial de comandos:

```bash
history
```

¿Porqué hemos ejecutado antes el comando 'ls -l /home/fabi'?

¿Y cómo podemos saber en qué dirfectorio estamos ahora mismo?

```bash
pwd

/home/fabi
```

- El directorio raíz `/` (*root directory*)
- Rutas absolutas y Rutas relativas
- ~
- .
- ..

Como se observa, las rutas en Linux se van componiendo usando la barra '/' (*slash*). En sistemas Windows, las rutas se expresan usando la barra invertida '\\' (*backslash*). Por eso en Windows las rutas son del tipo 'C:\\Usuarios\\Jacinto Flores'.

`Ejercicio`: Jugar con *ls* para ver qué hay en mi directorio y en mis directorios ascendentes.

Para cambiar de directorio usamos el siguiente comando:

```bash
cd /etc

# Vamos a comprobarlo:
pwd

/etc
```

Vayamos con una frase típica: "En Linux, todo son ficheros". Y además los ficheros no tienen extensión, o al menos no tienen porqué tenerla.

Es un sistema sensible a mayúsculas y minúsculas (*case-sensitive*).

¡Cuidado con los espacios y las tildes en los nombres de los ficheros y directorios!
  
  - Comillas dobles y simples
  - Escape de caracteres
  
Pero entonces, ¿todos los ficheros son iguales? La respuesta es que no. Hay diferentes tipos de ficheros:
  
```bash
file /etc
file /etc/hosts
file /bin/cp

type /etc
type ls
type cp
type type

which cp
```
  
`Algunos trucos`:
  
  - TAB Completion --> Escribir en el terminal "ls /et" y pulsar la tecla TAB (tabulador).
  - Historial de comandos --> En el terminal, pulsar las teclas UP y DOWN, e intentar recuperar un comando anterior del historial de comandos (incluso se puede modificar).
  - CTRL + R --> Búsqueda de comandos anteriores en el historial.
  - CTRL + L --> Borra la pantalla (equivale al comando `clear`).
  
Practiquemos un poco...

### Ayuda: Páginas del manual

Como en Linux hay muchos comandos, y es difícil recordar todas las opciones y argumentos de cada uno, existen unas páginas de ayuda, llamadas **manual** del sistema:

```bash
man ls

# Para salir de una página del manual, pulsamos la tecla 'q' (quit).
```

Dentro de una página del manual se pueden hacer búsquedas:
    - /
    - n

También se puede buscar una palabra en todas las páginas del manual:

```bash
man -k inode

# Busca en todas las páginas del manual la palabra "inode".
```

Podemos buscar más información sobre todos los comandos que hemos usado hasta ahora:

```bash
man exit

man ls

man echo

man history

man pwd

man cd

man file

man type

man which

# :-)
man man 
```

# Conexión de red
```bash
dhclient <Tarj.Red (normalmente 'enp0s3')>
```

---

## Enlaces de interés

Desde la web oficial de CentOS se recomiendan algunos [libros](https://wiki.centos.org/Books).

También hay muchos tutoriales, como por ejemplo [linuxcommand.org](http://linuxcommand.org)

En [YouTube](https://youtube.com), también podemos encontrar muchos contenidos acerca de CentOS y de sistemas Linux en general. Aquí dejamos algunos:

- [Installing Centos7 in Oracle VM VirtualBox](https://www.youtube.com/watch?v=Pcl417NR2xc)
- [Basic commands RHEL 7](https://www.youtube.com/watch?v=uDtXXD72T9M)
- [Shell scripting tutorial](https://www.youtube.com/watch?v=GtovwKDemnI)
- [Linux Tutorial for Beginners: Learn Red Hat Linux and CentOS](https://www.youtube.com/watch?v=D8x75-hgdeM)
- [Curso de CentOS 7](https://www.youtube.com/watch?v=cm7Sy_FGF00&list=PLn5IkU1ZhgiZobhGTWuX-tC5clz_rgdoh)
