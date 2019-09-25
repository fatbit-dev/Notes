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

```bash
cd

# Imprimir un texto en la salida estándar (típicamente la consola):
echo "aprendemos Linux!"
echo "saltos de línea \n\n\n"
echo -e "saltos de línea \n\n\n Bien!"

# Imprimir un texto en un fichero (sobreescribe el fichero, 
# y lo crea si no existía):
echo "nuevo contenido" > mi_
```
### Y
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

Los **_wildcards_** (o comodínes) nos permiten seleccionar nombres de fichero basado en patrones de caracteres.

| Wildcard       | Significado                                        |
| :------------- | :------------------------------------------------: |
|  *             |  Selecciona cualquier conjunto de caracteres.      |
|  ?             |  Selecciona un carácter cualquiera.                |
|  [caracteres]  |  Selecciona cualquier carácter que esté incluido en un conjunto de caracteres específicos.<br>Pueden expresarse como una *clase de caracteres POSIX* |
|  [!caracteres] |  Selecciona cualquier carácter que **NO** esté incluido en un conjunto de caracteres específicos. También pueden expresarse como una *clase de caracteres POSIX* |

Estas **Clases de caracteres POSIX** son las siguientes:

| Clase          | Significado                                         |
| :------------- | :-------------------------------------------------: |
|  [:alnum:]     |  Selecciona caracteres alfanuméricos.               |
|  [:alpha:]     |  Selecciona caracteres alfabéticos.                 |
|  [:digit:]     |  Selecciona caracteres numéricos.                   |
|  [:upper:]     |  Selecciona caracteres alfabéticos en *MAYÚSCULAS*. |
|  [:lower:]     |  Selecciona caracteres alfabéticos en *minúsculas*. |

Veamos algunos ejemplos para seleccionar nombres usando patrones de búsqueda y *wildcards*:

| Patrón                          | Significado                                         |
| :------------------------------ | :-------------------------------------------------: |
|  *                              |  Selecciona todos los nombres de fichero.                                                                   |
|  g*                             |  Selecciona los que empiezan con el carácter 'g'.                                  |
|  b*.txt                         |  Selecciona los que empiezan con el carácter 'b' y terminan con la cadena ".txt".  |
|  Data???                        |  Selecciona los que empiezan con la cadena "Data" y tienen exactamente 3 caracteres más.  |
|  [abc]*                         |  Selecciona los que empiezan con el carácter '**a**', o con el '**b**' o con el '**c**', seguidos de cualquier otra cadena.  |
|  [[:upper:]]*                   |  Selecciona los que empiezan con una MAYÚSCULA seguida de cualquier otra cadena.  |
|  BACKUP.[[:digit:]][[:digit:]]  |  Selecciona los que empiezan con la cadena "BACKUP." seguida por exactamente dos números.  |
|  *[![:lower:]]                  |  Selecciona cualquier fichero que no termine en una letra en minúscula.  |

### `cp` : Copiar ficheros (y directorios)

```bash
# Copia simple de ficheros (orgien -> destino):
cd ~
touch flower.txt

cp flower.txt flower_ORIG.txt
cp /etc/rsyslog.conf ~/rsyslog_FOR_REVIEW.conf
cp /etc/rsyslog.conf ~
ls -l
cat syslog_FOR_REVIEW.conf

# ¡CUIDADO! cp sobreescribe el fichero si ya existía:
cp /etc/fstab ~/rsyslog_FOR_REVIEW.conf
ls -l
cat syslog_FOR_REVIEW.conf

# Podemos usar la opción -i para pedir confirmación al usuario:
cp -i /etc/fstab ~/rsyslog_FOR_REVIEW.conf

# Copia un fichero a un directorio destino. El fichero en el destino mantiene
# el nombre del fichero original:
mkdir copias
cp /etc/rsyslog.conf ./copias/

# También podemos especificar un nombre para el fichero destino:
cp /etc/rsyslog.conf ./copias/rsyslog_COPY.conf

ls -l

# Copia un directorio (y todo su contenido) en otro directorio:
cp -R copias copias_2

ls -laR copias_2

# Podemos copiar muchos ficheros cuyos nombres sigan un patrón (usando wildcards):
touch presupuesto_1.txt
touch presupuesto_2.txt
touch presupuesto_3.txt

mkdir documentos_de_texto

cp *.txt documentos_de_texto

man cp
```

### `mv` : Mover/Renombrar ficheros y directorios

```bash
# Renombrar ficheros:
cd
touch tortilla.txt

mv tortilla.txt tortillaca.txt

# Mover ficheros o directorios a otro directorio:
touch tipos_de_cubiertos.txt
mkdir recetas
mkdir cosas_cocina

ls -l

mv tipos_de_cubiertos.txt cosas_cocina
mv recetas cosas_cocina

ls -l
ls -l cosas

# Mover varios ficheros/directorios a la vez:
touch tipos_de_sartenes.txt
touch tipos_de_cazuelas.txt
touch tipos_de_vajilla.txt

ls -l

mv tipos_de_sartenes.txt tipos_de_cazuelas.txt tipos_de_vajilla.txt cosas_cocina

ls -l
ls -l cosas

man mv
```

### `rm` : Eliminar ficheros (y directorios)

```bash
# Borrar ficheros:
touch document_01.txt
touch document_02.txt
touch document_03.txt
touch document_04.txt

ls -l

rm document_01.txt

ls -l

rm document_0?.txt

ls -l

# Borrar directorios:
mkdir juan
touch juan/datos.txt

ls -l juan

rm -r juan

ls -l juan
ls -l

# ¡CUIDADO! Cuando borramos algo con rm, se borra para siempre.

man rm
```

### `tar`: Comprimir ficheros (.tar, .tgz, .tar.gz)

```bash
cd
echo "Mi primer fichero" > compressed_file_1.txt
echo "Mi segundo fichero" > compressed_file_2.txt
echo "Mi tercer fichero" > compressed_file_3.txt

# Archiva varios ficheros/directorios en un fichero .tar
tar cvf mis_ficheros.tar compressed_file*.txt

ls -l

# Comprime varios ficheros/directorios en un fichero .tgz o .tar.gz
# Usa compresión GZIP
tar cvzf mis_ficheros_comprimidos.tgz compressed_file*.txt

ls -l

# Comprime un directorio en un fichero .tgz o .tar.gz
mkdir compress
mv compressed_file*.txt compress

tar cvzf mi_directorio_comprimido.tgz compress

ls -l

# Comprime varios ficheros en un fichero .bz2
# Usa compresión BZIP2
tar cvjf mi_directorio_mas_comprimido.bz2 compress

ls -l

# Extraer el contenido de un fichero .tar
tar xvf mis_ficheros.tar

# Extraer el contenido de un fichero .tgz o .tar.gz (GZIP)
tar xvzf mis_ficheros_comprimidos.tgz

# Extraer el contenido de un fichero .bz2 (BZIP2)
tar xvjf mi_directorio_mas_comprimido.bz2

# Listar el contenido de un fichero
tar tvf mis_ficheros.tar

# Opciones comunes de tar (flags):
#
#  -c : Crea un nuevo fichero
#  -v : Modo "verboso"
#  -f : Especifica el fichero de salida
#  -z : Comprime usando la herramienta gzip
#  -j : Comprime usando la herramienta bzip2
#  -x : Extrae el contenido de un fichero comprimido
#  -t : Lista el contenido de un fichero comprimido

man tar
info tar
```

### `zip` y ùnzip`: Comprimir ficheros (.zip)



### `date` y `cal` : La hora y el calendario



### `truncate` : Otra forma de vaciar un fichero

### `sed` : Edición de textos desde el terminal

### `tr` : 

### `grep` : Búsqueda de textos en ficheros y a la salida de comandos

### `find` : Buscando ficheros dentro de un directorio (y subdirectorios)

### `awk` : Un lenguaje de programación dentro de un comando

### `wc` : Calculadora en el terminal

## Redirecciones: Entrada estándar, Salida estándar, Salida de error

