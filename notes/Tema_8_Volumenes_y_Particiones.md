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

Este comando muestra si un disco tiene o no particiones. Aunque un disco no tenga particiones, puede que contenga datos. Esto puede comprobarse con el comando `blkid`, que muestra los sistemas de ficheros con los que se ha formateado cada partición de los discos:

```bash
blkid

```

Si un disco no aparece tras ejecutar el comando *blkid*, es que no tiene ningún sistema de ficheros.

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

En realidad, *fdisk* es una herramienta algo antigua, que por sus limitaciones, no puede usarse con discos de más de 2TB. Por eso hoy en día es más común usar `gdisk` o como `parted`, o alguna de sus variantes con entorno gráfico, como `gparted`.

## Algunos recursos útiles

- [Study guide - RedHat Certificate System Administrator](https://codingbee.net/rhcsa)
