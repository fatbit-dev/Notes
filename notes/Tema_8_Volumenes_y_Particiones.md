# Tema 8 - Volúmenes y Particiones

Este tema trata sobre cómo se manejan discos y particiones. El título habla de "volúmenes", porque es común que además de discos físicos y de sistemas de ficheros (particiones), existan volúmenes lógicos.

Un disco duro, tal y como viene de fábrica, no puede ser usado por un sistema operativo: antes debe ser formateado. En realidad no se formatea un disco, sino una partición del disco. Así, un disco puede dividirse en varias particiones, cada una con un formato diferente. 

Un volumen lógico es un disco virtual. Este disco virtual puede ser pequeño, y estar contenido dentro de una partición de un disco físico. O puede ser un disco virtual grande, que se reparte entre varios discos (o varias particiones en esos discos).

Además, existen varias formas de realizar las particiones en un disco: cada una de estas formas lleva asociada una *[tabla de particiones](https://es.wikipedia.org/wiki/Tabla_de_particiones)*, que se guarda en una zona física especial del disco, llamada habitualmente **MBR** (*[Master Boot Record](https://es.wikipedia.org/wiki/Registro_de_arranque_principal)*). Diferentes sistemas operativos y arquitecturas, pueden tener diferentes tipos de tablas de particiones: MBR, GPT, APM, etc.

Este tema es introductorio, y no se contemplan los volúmenes lógicos, aunque al final del tema se ofrecen algunos enlaces a recursos en línea.

**Importante**: Muchas de las operaciones para trabajar con discos y particiones, requiere de permisos de *root*.

## Dispositivos (discos)

Entre todos los dispositivos mostrados dentro del directorio `/dev`, se pueden encontrar los discos duros instalados en la máquina. Típicamente, los discos duros Serial ATA se nombran de forma similar a */dev/sda*, */dev/sdb*, */dev/sdc*, etc., mientras que los antiguos discos IDE se nombraban como */dev/hda*, */dev/hdb*, etc.

```bash
ls -l /dev | grep "sd[a-z]$"
ls -l /dev | grep "sd[a-z]"

```

Si detrás del nombre del disco (*sda*) aparece un número (como *sda2*), quiere decir que el disco ha sido particionado: existe al menos una partición. 

Para ver todos los discos (dispositivos de bloques) del sistema, se puede ejecutar el comando `lsblk`:

```bash
lsblk

```


### Particiones

Para gestionar particiones se usa el comando `fdisk`:

```bash
fdisk -l

fdisk -l /dev/sda1

```

Para crear una partición en un disco, se ejecuta:

```bash
fdisk /dev/sda

```

*fdisk* ofrece en su ayuda interactiva las siguientes opciones:

```text
Command (m for help): m
Command action
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition
   g   create a new empty GPT partition table
   G   create an IRIX (SGI) partition table
   l   list known partition types #
   m   print this menu
   n   add a new partition #
   o   create a new empty DOS partition table
   p   print the partition table #
   q   quit without saving changes
   s   create a new empty Sun disklabel
   t   change a partition's system id #
   u   change display/entry units
   v   verify the partition table
   w   write table to disk and exit #
   x   extra functionality (experts only)

```

El flujo de trabajo suele consistir en visualizar las particiones de un disco (p), mirar los tipos de particiones permitidos (l), crear una nueva particióndel tipo elegido (n), y escribir los cambios en el disco (w).

En realidad, *fdisk* es una herramienta algo antigua, que por sus limitaciones, no puede usarse con discos de más de 2TB. Por eso hoy en día, para manejar particiones en discos duros, es más común usar `gdisk` o como `parted`, o alguna de sus variantes con entorno gráfico, como `gparted`. Sí es común seguir usando *fdisk* para crear particiones en dispositivos de memoria *flash*, como las unidades USB o tarjetas *SD*.

### Sistemas de ficheros

Cuando se formatea una partición, se debe elegir un [sistema de ficheros](https://es.wikipedia.org/wiki/Sistema_de_archivos). En Linux existen multitud de sistemas de ficheros, algunos de los más comunes son:

- ext4
- xfs
- resiserfs
- vfat

Se puede consultar más información sobre los sistemas de ficheros en las páginas del manual:

```bash
man fs
man xfs
man ext4

```

### Formateo de particiones

Para formatear una partición de un disco, se usa la familia de comandos de `mkfs` (*make file-system*). Por ejemplo, se pueden usar los comandos *mkfs.cramfs*, *mkfs.ext3*, *mkfs.fat*, *mkfs.msdos*, *mkfs.xfs*, *mkfs.btrfs*, *mkfs.ext2*, *mkfs.ext4*, *mkfs.vfat*, etc.

Por ejemplo, para formatear la partición */dev/sdb1* con formato *XFS*, se ejecutaría el siguiente comando:

```bash
# El flag -L es opcional, pero nos permite otorgar un nombre a la partición.
mkfs.xfs -L "MyDisk" /dev/sdb1

```

Cuando se formatea una partición, el sistema operativo asigna un identificador único para ese dispositivo (partición): es el `UUID`. El comando `blkid` muestra un listado de todos los dispositivos formateados, y ahí se puede ver el *UUID* asignado a cada disco.

Si un dispositivo no aparece en el listado anterior, es porque no ha sido formateado.

Para saber el sistema de ficheros de una partición, se puede usar el comando `file`:

```bash
file -sL /dev/sdb1

```

Existe otra forma de particionar discos, muy común cuando se trabaja con máquinas virtuales, que son los **volúmenes lógicos** o **LVM** (*Logical Volume Manager*). Mediante *LVM* se pueden crear particiones que abarquen varios discos, y también permite operaciones como aumentar o disminuir el tamaño de los volúmenes (particiones) en caliente. En esta asignatura no vamos a trabajar con *LVM*, pero se recomienda leer más información acerca de esta forma de formatear unidades.

```bash
man mkfs
man mkfs.ext3
man mkfs.ext4
man mkfs.xfs
man mkfs.vfat

```

### Borrar una partición

Para borrar una partición, se puede usar el comando `fdisk` visto anteriormente. Sin embargo, con esta acción se borra la información relativa a ese sistema de ficheros (se borra su entrada de la tabla de particiones del disco). Pero los datos siguen en el disco, y podrían ser recuperados mediante técnicas de análisis forense de datos.

Para eliminar el contenido de un disco, antes de borrar la partición, conviene eliminar los datos, y para ello es común usar `/dev/zero`:

```bash
cat /dev/zero > /dev/sdb1

```

Nótese que cuando se borran los datos de un disco, o cuando se borra su sistema de ficheros, la partición permamece intacta. Como se ha comentado, para borrar definitivamente la partición debería usarse *fdisk*, y el espacio después de borrar sería espacio libre en el disco (pero sin particionar, por lo que no puede ser usado hasta que se cree una nueva partición).

### Tabla de particiones

Cada unidad de disco posee una zona especial en la que se definen las diferentes particiones que contiene el disco, de qué tipo son estas particiones, dónde empiezan y acaban, etc. Entre los tipos de tablas de particiones más comunes se pueden encontrar **MBR** y **GPT**. Antes se ha visto cómo listar el contenido de una partición *MBR* usando *fdisk*. Para trabajar con particiones de tipo *GPT*, se usa el comando `gdisk`:

```bash
gdisk -l /dev/sdc

```

En cualquier caso, las versiones modernas de *fdisk* también permiten trabajar con particiones *GPT*, aunque muchos administradores de sistemas siguen usando *gdisk*.


### Parted

En realidad *fdisk* es un programa algo antiguo, que tenía una limitación para trabajar con unidades de 4TB como máximo. Para salvar esas limitaciones, se creó el programa `parted` (que puede trabajar tanto con tablas de particiones *MBR* como *GPT*). Por ejemplo, para listar la tabla de particiones con *parted* se ejecuta el siguiente comando:

```bash
parted /dev/sda p

```

## Algunos recursos útiles

- [Hard disk layout](https://developer.ibm.com/tutorials/l-lpic1-102-1/)
- [Create partitions and file-systems](https://developer.ibm.com/technologies/linux/tutorials/l-lpic1-104-1)
