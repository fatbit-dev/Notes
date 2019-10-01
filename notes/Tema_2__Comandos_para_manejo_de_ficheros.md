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
echo "nuevo contenido" > mi_fichero.txt
```

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

### `zip`, `gzip`, `bzip2` y `tar`: Comprimir ficheros y directorios

```bash
# Preparamos unos ficheros de prueba
cd
mkdir -p "$HOME/docus"
cd "$HOME/docus"
echo "Este es el documento 1" > doc1.txt
echo "Este es el documento 2" > doc2.txt
echo "Este es el documento 3" > doc3.txt
echo "Este es el documento 4" > doc4.txt
echo "Este es el documento 5" > doc5.txt

cat doc1.txt doc2.txt doc3.txt doc4.txt doc5.txt > big_doc.txt
cp big_doc.txt  big_doc_to_gzip.txt
cp big_doc.txt  big_doc_to_bzip2.txt

pwd
ls -l

# zip
zip docus.zip doc[[:digit:]].txt
ls -l

# gzip
# gzip doc[[:digit:]].txt
gzip big_doc_to_gzip.txt
ls -l

# bzip2
# bzip2 doc[[:digit:]].txt
bzip2 -k doc[[:digit:]].txt
bzip2 big_doc_to_bzip2.txt
ls -l

# El comando tar se ha visto antes, pero se deja aquí una referencia rápida.
# tar: usando gzip
tar zcvf docus.tgz doc[[:digit:]].txt 
tar zcvf docus.tar.gz doc[[:digit:]].txt 
ls -l

# tar: usando bzip2
tar jcvf docus.tar.bz2 doc[[:digit:]].txt
ls -l

# Para listar el contenido de un fichero comprimido, se usan estos comandos.
unzip -l docus.zip
gzip -l doc1.gz
tar ztvf docus.tgz
tar ztvf docus.tar.gz
tar jtvf docus.tar.bz2


man zip
man gzip
man bzip2
man tar
```

### `uzip`, `gunzip`, `bunzip2` y `tar`: Comprimir ficheros y directorios

```bash
# Usamos los ficheros de prueba empleados al comprimir.
cd "$HOME/docus"

# unzip
unzip docus.zip 
ls -l

# gzip
gzip -d big_doc_to_gzip.gz
gunzip big_doc_to_gzip.gz
ls -l

# bzip2
bzip2 -d big_doc_to_bzip2.bz2
bunzip2 big_doc_to_bzip2.bz2
ls -l

# El comando tar se ha visto antes, pero se deja aquí una referencia rápida.
# tar: usando gunzip
tar zxvf docus.tgz 
tar zxvf docus.tar.gz 
ls -l

# tar: usando bzip2
tar jxvf docus.bz2
ls -l

man unzip
man gunzip
man bunzip2
man tar
```

### `date` y `cal` : La hora y el calendario

```bash
# Mostrar la fecha y hora
date
date -u
date +"%Y"
date +"%m"

date +"%d-%m-%Y"
date +"%d-%m-%Y %H:%M:%S"

# Establecer la fecha y la hora (necesita permisos de root)
date --set 2019-10-02
date --set 19:00:07
date --set 2019-10-02 19:00:07

# Mostrar el calendario
cal
cal -3m

# Gestión de la fecha y hora a la CentOS 7 (RedHat)
timedatectl

## timedatectl: establecer la fecha y la hora
timedatectl set-time 22:53:48
timedatectl set-time "2019-10-02 19:00:07"
timedatectl set-time "2019-10-02"

## timedatectl: listar las zonas horarias
timedatectl list-timezones
timedatectl list-timezones | grep -i madrid
timedatectl list-timezones | grep -i europe

## timedatectl: establecer la zona horaria
timedatectl set-timezone Europe/Madrid

# Des/Activar la sincronización de la fecha y hora por NTP
timedatectl set-ntp no
timedatectl set-ntp yes

man date
man cal
man timedatectl
```

### `truncate` : Otra forma de vaciar un fichero

```bash
truncate -s 0 mi_fichero.txt

# Y hay más formas...

> mi_fichero.txt

echo > mi_fichero.txt

true > mi_fichero.txt

cp /dev/null mi_fichero.txt

```

### `grep` : Búsqueda de textos en ficheros y a la salida de comandos

```bash
# Buscar una expresión en un fichero
grep "fabi" /etc/group
grep "fabi" /etc/passwd

# Buscar una expresión en múltiples ficheros, de forma recursiva (-r)
cd /etc
grep -r "fabi" *

# Buscar una expresión en un fichero mostrando los números de línea (-n)
grep -rn "fabi" *
cat /etc/group | grep -n "fabi"

# Buscar una expresión a la salida de otro comando
date --help
date --help | less

date --help | grep "%"

# Buscar sin considerar las MASYÚSCULAS/minúsculas (-i)
ps aux
ps aux | grep -i "Cron"

cat /etc/group | grep -ni "FABI"

# Excluir una expresión en una búsqueda (-v)
ps aux | grep -i "cron" | grep -v "grep"
ps aux | grep -i "cron" | grep -vi "grep"

# Excluir varias palabras de una búsqueda
cat /etc/passwd

grep -v -e "fabi" -e "nologin" /etc/passwd
grep -vE "fabi|nologin" /etc/passwd

history | grep "ls" | grep -v -e "-l" -e "-a" -e "~"
history | grep "ls" | grep -vE "-l|-a|~"
# Hay que 'escapar' el primer signo -
history | grep "ls" | grep -vE "\-l|-a|~"

```

### `find` : Buscando ficheros dentro de un directorio (y subdirectorios)

```bash
cd

# Buscar un nombre de fichero (-name)
find . -name "doc1.txt"
find . -name "doc*.txt"
find . -name "doc*.txt"

# Buscar un nombre de fichero sin diferenciar entre MAYÚSCULAS/minúsculas (-iname)
find . -iname "DOC*.txt"

# Buscar todos los nombres de fichero excepto los que se indiquen (-not)
find . -not -name "doc*.txt"

# Buscar y borrar (¡CUIDADO!)
find . -name "big_doc_to_bzip2.bz2" -delete

# Buscar por tipo de fichero (-type [f d l c b])
find / -type d
find ~ -type d
find ~ -type f -name "doc*"
find ~ -type d -name "doc*"

# Buscar por fecha (-atime, -mtime, -ctime)
#
# En Linux cada fichero posee metadatos relativos a 3 fechas:
#
#  - Tiempo de acceso (-atime) – Fecha de la última lectura o escritura del
#    fichero.
#  - Tiempo de modificación (-mtime) – Fecha de la última modificación del 
#    fichero.
#  - Hora de cambio (-ctime) – Fecha de la última actualización de los 
#    metadatos del archivo (como cuando se hace touch).
#
# Esta opción se usa con un número, que especifica el número de días desde 
# que se accedió, modificó o cambió el fichero. 

find ~ -atime 1
find ~ -mtime +2
find ~ -ctime -1

## Para buscar ficheros modificados hace menos de un minuto: ( -[amc]min )
find ~ -mmin -1

# Buscar por tamaño (exacto) (-size)
find / -size 10M

## Otras búsquedas por tamaño
find ~ -size +5G
find ~ -size -1k

# Buscar por propietario o grupo (-user y -group)
find /tmp -user fabi
find /tmp -group fabi

# Búsqueda por permisos (exacta).  
find ~ -perm 644

# Búsqueda por permisos (como mínimo 644).  
find ~ -perm -644

# Algunas otras opciones útiles
## Buscar ficheros y directorios vacíos
find ~ -empty

## Buscar ejecutables
find / -exec

## Buscar legibles
find / -read

```

### `awk` : Una herramienta avanzada para el tratamiento de textos

`awk` es en realidad un lenguaje de programación interpretado. *awk* lee una
línea de la entrada y la va procesando (procesa línea a línea). Todas las 
instruciones que hacemos con *awk* se van aplicando secuencialmente a la
entrada (a la línea leída).

Es una herramienta muy potente, pero con cierta complejidad. Aquí sólo se 
muestran unos pocos usos comunes.

```bash
# Mostrar el contenido de un fichero
cd
echo "1) John       Maths         6.54" > notes.txt
echo "2) Paul       Physics       8.23" >> notes.txt
echo "3) Michael    Biology       7.98" >> notes.txt
echo "4) Steve      Physics       5.10" >> notes.txt
echo "5) Jack       History       4.68" >> notes.txt
echo "6) Richard    Programming   9.05" >> notes.txt
echo "7) John       Programming   8.42 - this note should be reviewed" >> notes.txt

ls -l
cat notes.txt

awk '{print}' notes.txt

# Imprimir columnas (o campos)
awk '{print $1}' notes.txt
awk '{print $2"\t"$3}' notes.txt
awk '{print $2"\t\t"$3}' notes.txt

# Imprimir las filas que cumplan un patrón
awk '/Phy/ {print}' notes.txt

# Imprimir las columnas que cumplan un patrón
awk '/Phy/ {print $1}' notes.txt
awk '/Phy/ {print $2"\t\t"$3}' notes.txt

# Contar e imprimir el número de filas que cumplen un patrón
awk '/Phy/{++counter} END {print "Cuenta = ", counter}' notes.txt

# Imprimir las filas que superan los 30 caracteres
awk 'length($0) > 35' notes.txt

# Especificar otro carácter como separador de campos
echo "10;20;30;40;50;60;70" > scale.csv
cat scale.csv | awk -F ";" '{print $1}'
cat scale.csv | awk -F ";" '{print $1"\t"$2"\t"$7}'

# Es muy útil para tratar la salida de otros comandos
ls -la | awk '{print $5}'

man awk
info awk
```

### `sed` : Edición de textos desde el terminal

`sed` es un editor de flujos (**s**tream **ed**itor). Al igual que *awk*
trata cada línea de su entrada. Por eso se suele usar para editar ficheros
o leer la salida de algún comando, sustituyendo un texto (o patrón) por
otro.

```bash
# Substitución de un patrón por otro patrón
echo "Es un poema de Antonio" | sed 's/Antonio/Federico/'
echo "Es un poema de Antonio" | sed 's/nton/urel/'

# ¡Atención! Por defecto, sed sólo modifica la primera ocurrencia del 
# patrón:
echo "one two three, one two three" > example.txt
echo "four three two one" >> example.txt
echo "one hundred" >> example.txt
cat example.txt

cat example.txt | sed 's/one/ONE/'

# Para reemplazar TODAS las ocurrencias, se usa la opción de reemplazo global:
cat example.txt | sed 's/one/ONE/g'

# Se puede especificar otro delimitador diferente a '/'
echo 'PATH=$PATH:/usr/local/bin' > wrong_path.txt
cat wrong_path.txt

# Si la cadena a reemplazar contiene '/', tenemos que escapar cada '/'
# para que sed no las confunda con una de las que él necesita.
cat wrong_path.txt | sed 's/\/usr\/local\/bin/\/opt\/bin/'

# Esto se puede evitar cambiando el símbolo delimitador:
cat wrong_path.txt | sed 's_/usr/local/bin_/opt/bin_'
cat wrong_path.txt | sed 's:/usr/local/bin:/opt/bin:'
cat wrong_path.txt | sed 's|/usr/local/bin|/opt/bin|'
cat wrong_path.txt | sed 's+/usr/local/bin+/opt/bin+'

# sed se usa mucho para modificar ficheros ya existentes
## Generando un nuevo fichero con los cambios:
sed 's/\/usr\/local\/bin/\/opt\/bin/' <wrong_path.txt >right_path.txt
cat wrong_path.txt
cat right_path.txt

## Sobreescribiendo el fichero original, pero con los nuevos cambios: (-i)
cat wrong_path.txt
sed -i 's/\/usr\/local\/bin/\/opt\/bin/' wrong_path.txt
cat wrong_path.txt

# Se puede usar sed para modificar varias expresiones simultáneamente:
cat example.txt | sed -e 's/one/ONE/' -e 's/two/TwO/'
cat example.txt | sed -e 's/one/ONE/g' -e 's/two/TwO/'
cat example.txt | sed -e 's/one/ONE/' -e 's/two/TwO/g'
cat example.txt | sed -e 's/one/ONE/g' -e 's/two/TwO/g'

# También se pueden modificar sólo las líneas que además contengan otro
# patrón:
cat example.txt | sed -e '/hundred/s/one/ONE/'

# Se pueden buscar patrones para reemplazar, independientemente de las
# MAYÚSCULAS/minúsculas:
cat example.txt | sed -e 's/oNe/ONE/I'

# Por último, se pueden agrupar varias opciones a la vez:
cat example.txt | sed -e 's/oNe/ONE/Ig'

man sed
info sed
```

### `tr` : 

### `cut` :

### `wc` : Calculadora en el terminal

`wc`es una utilidad que permite contar el número de líneas, el número de palabras y el número de bytes de un fichero, o de las líneas que reciba por su entrada.

```bash
# Número de líneas, número de palabras, y número de bytes
wc /etc/group

# Para contar el número de líneas
wc -l /etc/group

# Para contar el número de bytes
wc -c /etc/group

# Para contar el número de caracteres (bytes)
wc -m /etc/group

# Para contar el número de palabras
wc -w /etc/group

# Se puede usar en combinación con otros comandos
ls -1 /etc | wc -1

man wc
```

## Redirecciones: Entrada estándar, Salida estándar, Salida de error

## Algunos recursos útiles

- [Linux Filesystem](https://www.linux.com/tutorials/linux-filesystem-explained/)
- [Tutorial de awk](https://www.grymoire.com/Unix/Awk.html)
- [Tutorial de sed](https://www.grymoire.com/Unix/Sed.html)
- [Servidor TeamSpeak3](https://www.hostinger.es/tutoriales/crear-servidor-ts3-teamspeak-3)
