# Tema 6 - Gestión de paquetes software con YUM

Este tema trata sobre cómo gestionar los programas en Linux CentOS 7, usando `YUM`. Pero la teoría y filosofía de trabajo es aplicable a cualquier sistema Linux, aunque se basen otras herramientas, como RPM o APT. Se aprenderá a:

- Instalar, reinstalar y actualizar paquetes usando YUM.
- Obtener información de los paquetes instalados, incluyendo: versión, estado, 
  dependencias e integridad.
- Determinar qué ficheros proporciona un paquete, así como encontrar de qué 
  paquete proviene un determinado fichero.

En los primeros días de Linux, muchos programas se distribuían en código fuente. El usuario tenía que compilar este código fuente para crear los programas correspondientes (binarios), así como la actualización de las páginas *man* y modificaciones en los archivos de configuración. Actualmente las distribuciones Linux (*distros*) proporcionan conjuntos de programas llamados paquetes (*packages*), listos para su instalación.

Este tema se centra en **YUM** (*Yellowdog Updater Modified*), usado por CentOS, aunque también se soporta RPM (RedHat Package Manager). Los Linux basados en Debian usan APT.  

Durante la vida de un sistema Linux, es fácil que el usuario quiera instalar nuevas funcionalidades a su sistema, borrar o actualizar los paquetes originales.  

## Instalando paquetes (yum install)

Para saber si tenemos un paquete (xx) instalado tenemos varias opciones:

```bash
# Podemos invocarle directamente
nano
nano --help
# no se encontró la orden

# Podemos ver si existe su página de manual
man nano
# No manual entry for nano

# Podemos buscar si está en el PATH
which nano
# no nano in (/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin:/bin:/sbin:)

# O intentar saber qué tipo de fichero es
type nano
# type: nano: no se encontró
```

Si se obtiene una repuesta de que no se encuentra, es que no está instalado.

El comando para instalar un paquete es `yum install`. Para ejecutar el comando *yum*, y sus diferentes opciones, primero hay que ser superusuario. Si se pide confirmación de instalación, responder *y*. Entonces de descarga los paquetes.

Si aparece una confirmación final, en este caso *¡Listo!*, el paquete ha sido descargado e instalado correctamente  

```bash
su -

yum install vim
Is this ok [y/d/N]: y
¡Listo!

exit
 
```
### Repositorios

YUM, al igual que APT, trabaja con **repositorios**, que son conjuntos de paquetes a los que, típicamente, se accede mediante una conexión de red.

**Localización de los paquetes**

Tecleando un *ls -la /etc* podemos ver los directorios *`/etc/yum.repos.d/`*, *`/etc/yum/`* y el fichero informativo *`yum.conf/`*. Todos estos ficheros y directorios guardan información y configuración de yum.

Los paquetes se instalan, por defecto, en el directorio *`/etc/yum.repos.d/`* que contiene varios ficheros que son repositorios (*\*.repo*). Los *repos* se dividen en tres secciones: paquetes normales, paquetes de desarrollo (debug) y paquetes en código fuente. El *repo* dice a CentOS en que *mirrors* (o réplicas) están las últimas versiones de los paquetes.

```bash
ls -l /etc
ls -l /etc/yum.repos.d
```

## Desinstalando paquetes con YUM (yum remove)

Para desinstalar paquetes se usa el comando *`yum remove`* como superusuario. Si el paquete a eliminar se utiliza por otros paquetes, se ofrece la posibilidad de desinstalar los paquetes dependientes.

```bash
su -

yum remove gfortan
# ............  Resolving dependencies
# ............. Dependencies resolved
# Is this ok [y/N]

```

## Actualizando paquetes con YUM (yum update)

Para actualizar paquetes se usa el comando  *`yum update ´nombre de paquete´`*. Si no se especifica ningún paquete, *yum* entiende que se quiere actualizar el sistema entero.

```bash
su -

yum update ´gfortan´
# ............
# Resolving dependencies
# ................
# Dependencies resolved
# Is this ok [y/N] y
# Complete!

```

Es posible actualizar a una versión determinada con *`yum update_to xx.yy.zz`*:

```bash
su -

yum update_to xx.yy.zz ´gfortan´

```

## Información de paquetes (yum info)

Para obtener paquetes con yum el comando es *`yum info ´nombre de paquete´`*. 

Si no se especific ningún paquete, *yum* entiende que se quiere información sobre el sistema entero. Para obtener información de paquetes no es necesario ser superusuario.
 
```bash
yum info gfortran
 
```

## Comprobar actualizaciones (yum check-update)

Es posible consultar si un paquete, o el sistema entero, tiene todas las actualizaciones instaladas, o bien si hay paquetes con actualizaciones. Los valores posibles que devuelve *yum check-update* son:

- **100**: Significa que hay paquetes disponibles para una actualización. También se presenta una lista de paquetes con actualizaciones pendientes. 
- **0**: No hay actualizaciones disponibles.
- **1**: Si hay errores. 

```
yum check-update gfortran
 
yum check-update

```

## Búsqueda de paquetes (yum search)

Se puede buscar un término entre el nombre o la descripción de todos los paquetes disponibles en los repositorios. Para ello se usa el comando *`git search`*:

```bash
yum search pyqt

```

## Haciendo limpieza (yum clean)

Los gestores de paquetes suelen utilizar cachés para almacenar información sobre los paquetes. Estas cachés ocupan espacio en disco, y pueden ser vaciadas para liberar espacio. El comando para limpiar estas cachés y metadatos es *`yum clean all`*.

```bash
yum clean
yum clean all

```

## Algunos recursos útiles

Se recomienda leer la página de manual de *yum*, donde se puede encontrar información detallada de todas sus opciones:

```bash
man yum
info yum

```
