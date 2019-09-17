# Tema 2 - Comandos para manejo de ficheros

## Ficheros y Directorios

Un directorio en linux es un fichero especial, que puede contener otros ficheros y sub-directorios.

```bash
file /etc
```

### `mkdir` : Creación de directorios

```bash
cd ~
mkdir projects
ls -la projects

mkdir projects/games/super_pang

# La opción -p crea los padres (ancestros) si no existen
mkdir -p projects/games/super_pang
mkdir -p projects/games/counter_strike
ls -laR projects

man mkdir
```

### `rmdir` : Borrado de directorios

```bash
cd ~
rmdir projects/games
rmdir projects/games/counter_strike

man rmdir
```

### `touch` : Creación y actualización de ficheros

```bash
cd ~
touch projects/description.txt
touch projects/Description.txt

man touch
```

### `cat` : Mostrar el contenido de un fichero

```bash
cat /etc/profile
```

### `less` : Mostrar el contenido de un fichero, con vitaminas

```bash
less /etc/profile

# ESPACIO
# ENTER
# b
# G
# 1G
# /palabra
#   n
# q

man less
```

### `tail` : Mostrar las últimas líneas de un fichero

```bash
tail /etc/rsyslog.conf
tail -n 5 /etc/rsyslog.conf
tail -f /var/log/messages

man tail
```

### `head` : Mostrar las primeras líneas de un fichero (cabecera)

```bash
head /etc/rsyslog.conf
head -n 5 /etc/rsyslog.conf

man head
```

### `echo` : Imprimir en la consola y en ficheros


### `ln` : Enlaces

Son parecidos a los accesos directos de Windows.

```bash
ls -l /boot

ls -l /lib
```

Veamos cómo crear enlaces:

```bash
cd ~

touch document.txt
mkdir softlinks
cd softlinks
ln -s ../document.txt
ln -s /home/fabi/document.txt ./my_document.txt

man ln
```

### *Wildcards* : trabajando con múltiples ficheros a la vez

Son unos caracteres especiales que sirven para especificar grupos de ficheros a partir de sus nombres.

Los **wildcards** nos permiten seleccionar nombres de fichero basado en patrones de caracteres.

| Wildcard       | Significado                                        |
| :------------- | :------------------------------------------------: |
|  *             |  Selecciona cualquier conjunto de caracteres.      |
|  ?             |  Selecciona un carácter cualquiera.                |
|  [caracteres]  |  Selecciona cualquier carácter que esté incluido en un conjunto de caracteres específicos. Pueden<br>expresarse como una *clase de caracteres POSIX* |
|  [!caracteres] |  Selecciona cualquier carácter que **NO** esté incluido en un conjunto de caracteres específicos. También pueden expresarse como una *clase de caracteres POSIX* |

Estas **Clases de caracteres POSIX** son las siguientes:

| Clase          | Significado                                         |
| :------------- | :-------------------------------------------------: |
|  [:alnum:]     |  Selecciona caracteres alfanuméricos.               |
|  [:alpha:]     |  Selecciona caracteres alfabéticos.                 |
|  [:digit:]     |  Selecciona caracteres numéricos.                   |
|  [:upper:]     |  Selecciona caracteres alfabéticos en *MAYÚSCULAS*. |
|  [:lower:]     |  Selecciona caracteres alfabéticos en *minúsculas*. |

### `cp` : Copiar ficheros (y directorios)



### `mv` : Mover/Renombrar ficheros y directorios



### `rm` : Eliminar ficheros (y directorios)


### `tar`: Comprimir ficheros (.tar, .tgz, .tar.gz)


### `zip` y ùnzip`: Comprimir ficheros (.zip)


## Redirecciones: Entrada estándar, Salida estándar, Salida de error

