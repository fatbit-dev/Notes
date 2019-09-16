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
```

## Redirecciones: Entrada estándar, Salida estándar, Salida de error

