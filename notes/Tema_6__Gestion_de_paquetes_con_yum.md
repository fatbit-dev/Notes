# Tema 6 - Gestión de paquetes software con YUM

Este tema trata sobre cómo gestionar los programas en Linux CentOS 7, usando `YUM`. Pero la teoría y filosofía de trabajo es aplicable a cualquier sistema Linux, aunque se basen otras herramientas, como RPM o APT. Se aprenderá a:

- Instalar, reinstalar y actualizar paquetes usando YUM.
- Obtener información de los paquetes instalados, incluyendo: versión, estado, 
  dependencias e integridad.
- Determinar qué ficheros proporciona un paquete, así como encontrar de qué 
  paquete proviene un determinado fichero.

En los primeros días de Linux, muchos programas se distribuían en código fuente. El usuario tenía que compilar este código fuente para crear los programas correspondientes (binarios), así como la actualización de las páginas *man* y modificaciones en los archivos de configuración. Actualmente las distribuciones Linux (*distros*) proporcionan conjuntos de programas llamados paquetes (*packages*), listos para su instalación.

Este tema se centra en la herramienta **YUM** (*Yellowdog Updater Modified*), usado por CentOS, aunque también se puede usar la herramienta RPM (RedHat Package Manager). Los Linux basados en Debian usan otra herramienta diferente, llamada *APT*.  

Las instalaciones basadas en *YUM* presentan la ventaja, sobre las instalaciones basadas en *RPM*, que *YUM* analiza las dependencias (*dependencies*) con otros paquetes, y si se necesitan paquetes adicionales, invita a su instalación. Dicho de otra manera, *YUM* resuleve las dependencias de los paquetes a instalar de forma más eficiente que *RPM*.

Durante la vida de un sistema Linux, es fácil que el usuario quiera instalar nuevas funcionalidades a su sistema, borrar o actualizar los paquetes originales.  

## Instalando paquetes (yum install)

Para saber si tenemos un paquete (xx) instalado tenemos varias opciones:

```bash
# Podemos invocarlo directamente
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

yum install gfortran
Is this ok [y/d/N]: y
¡Listo!

exit
 
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

yum update gfortan
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

yum update_to xx.yy.zz gfortan

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

Se puede buscar un término entre el nombre o la descripción de todos los paquetes disponibles en los repositorios. Para ello se usa el comando *`yum search`*:

```bash
yum search pyqt

```

## Haciendo limpieza (yum clean)

Los gestores de paquetes suelen utilizar cachés para almacenar información sobre los paquetes. Estas cachés ocupan espacio en disco, y pueden ser vaciadas para liberar espacio. El comando para limpiar todas estas cachés y metadatos es *`yum clean all`*.

```bash
yum clean
yum clean all

```

## Reinstalar (yum reinstall)

A veces, puede fallar la instalación de algún paquete software. En estos casos se puede intentar reinstalar únicamente el paquete cuya instalación falló.

```bash
yum reinstall gfortran
```

### Repositorios

YUM, al igual que APT, trabaja con **repositorios**, que son conjuntos de paquetes a los que, típicamente, se accede mediante una conexión de red.

**Localización de los paquetes**

Ejecutando *ls -la /etc* podemos ver los directorios *`/etc/yum.repos.d/`*, *`/etc/yum/`* y el fichero informativo *`yum.conf/`*. Todos estos ficheros y directorios guardan información y configuración de yum.

La información de los paquetes instalados se guarda, por defecto, en el directorio *`/etc/yum.repos.d/`* que contiene varios ficheros que contienen información sobre repositorios (*\*.repo*). La información de los *repos* se dividen en tres secciones: paquetes normales (CentOS-Base.repo), paquetes de desarrollo (CentOS-Debuginfo.repo) y paquetes en código fuente (CentOS-Sources.repo). El *repo* especifica en que *mirrors* (o réplicas) están las últimas versiones de los paquetes.

```bash
ls -l /etc
ls -l /etc/yum.repos.d
```

A veces se quiere instalar un programa que no está incluido en los repositorios oficiales de CentOS (en sus repositorios por defecto). Para ello, se pueden añadir nuevos repositorios de software.

## Añadir nuevos repositorios

Para añadir un repositorio debemos crear un fichero con extension *.repo*. Típicamente estos ficheros se crearán dentro del directorio *`/etc/yum.repos.d/`*.

Para añadir un repositorio, o para instalar paquetes en general, se necesitan permisos de super-usuario (*root*).

Después, mediante un editor como *vim* o *nano*, se crea un fichero con nombre *nombre_repositorio.repo*, en el que se incluyen los siguientes campos:

```text
[nombre_repositorio]
name=Nombre del repositorio
baseurl=http://servidor/repositorio
enabled=1
gpkey=http:/servidor/repositorio/gpgkey
gpgcheck=1
```

- *name* indica el nombre.
- *baseurl* es la ruta (URL) hasta el repositorio.
- *enabled* habilita (1) o deshabilita (0) el repositorio.
- *gpgkey* es la ruta hacia la llave de seguridad (clave GPG).
- *gpgcheck* comprueba la integridad con la llave (1) o ignora la integridad de la llave (0).

Una vez guardado el fichero *.repo* se pueden listar los repositorios.

```bash
yum repolist
```
Se debe obtener un listado similar al siguiente:

```text
Complementos cargados:fastestmirror
Loading mirror speeds from cached hostfile
* base: mirror.librelabucm.org
* extras: mirror.librelabucm.org
* update. mirror.librelabucm.org
*id del repositorio		nombre del repositorio
!base/7/x86_64			CentOS-7 - Base
!extras/7/x86_64		CentOS-7 - Extras
!updates/7/x86_64		CentOS-7 - Updates
!n_repositorio/7/x86_64	CentOS-7 - n_repositorio
 
```

Cuando se activa un nuevo repositorio, conviene actualizar la información de *yum*:

```bash
yum check-update
```

A continuación se instalaría el paquete deseado, que está en el nuevo repositorio:

```bash
yum install nombre_paquete
```

## Activar repositorios adicionales de CentOS

Para CentOS 7 se recomienda habilitar los repositorios **opcional**, **extra** y **HA**, ya que a veces contienen software más actualizado que los repositorios base (y soluciona algunos problemas de dependencias de paquetes):

```bash
subscription-manager repos --enable "rhel-*-optional-rpms" --enable "rhel-*-extras-rpms"  --enable "rhel-ha-for-rhel-*-server-rpms
```

## Repositorio adicional para Linux Empresarial (EPEL)

EPEL (*Extra Packages for Enterprise Linux*) es un Grupo de Interés Especial (*SIG*) de la distribución *Fedora*, que crea, mantiene y gestiona un conjunto de paquetes para sistemas operativos Linux derivados de RedHat.  

La lista de los paquetes EPEL disponibles para CentOS 7, para arquitecturas x86_64, se pueden encontrar en:

[https://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/a/](https://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/a/)

Como este *repo* es tan popular, la forma de instalarlo y activarlo está simplificada con respecto a lo comentado anteriormente. Para instalar (activar) este repositorio y dejarlo configurado en el sistema, se puede ejecutar el siguiente comando:

```bash
yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
```

## Repositorio adicional para instalar una versión moderna de git (Wandisco Git repositry)

Actualmente, los repositorios de CentOS 7 proporcionan una versión muy antigua de *git*, la 1.8.3.

La forma más facil de instalar una versión de *git* moderna (como la 2.18), es instalarlo con *yum* desde los repositorios *Wandisco*.

En primer lugar hay que activar el repositorio *Wandisco Git*. Para esto hay que crear el fichero *`wandisco-git.repo`* en el directorio *`/etc/yum.repos.d`*.

```text
nano /etc/yum.repos.d/wandisco-git.repo
```

El contenido de este fichero será:

```text
[wandisco-git]
name=Wandisco GIT Repository
baseurl=http://opensource.wandisco.com/centos/7/git/$basearch/
enabled=1
gpgcheck=1
gpgkey=http://opensource.wandisco.com/RPM-GPG-KEY-WANdisco
```

Después, hay que importar las llaves *GPG* para este repositorio:

```bash
rpm --import http://opensource.wandisco.com/RPM-GPG-KEY-WANdisco

yum check-update
```

Una vez que el repositorio se ha añadido, se puede instalar *git* con normalidad, usando *yum*:

```bash
yum install git
```

Para verificar la instalación, podemos preguntarle a Git su versión:
```bash
git --version
```

### Configurar git

Una vez instalado *git*, se puede configurar un usuario (esta información aparecerá en un *repo* de *git* cuando se hagan *commits*:

```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tucorreo@tudominio.com"
```

Para comprobar los datos introducidos:

```bash
git config --list"

# Output
# user.name=Tu Nombre
# user.email=Tucorreo@tudominio.com
```

La información anterior se guarda en el fichero *`$HOME/.gitconfig`*.

## Listar los paquetes que contiene un repositorio en particular

Para ver los paquetes de software que proporciona un repositorio, se puede ejecutar un comando similar a este (que muestra los paquetes del repositorio *EPEL*):

```bash
yum list available --disablerepo=* --enablerepo=epel
```
## Instalar paquetes desde un repositorio en particular

Puede ocurrir que varios repositorios ofrezcan los mismos paquetes software, quizá con versiones diferentes. Se puede elgir instalar un paquete desde un repositorio en particular, ignorando los paquetes que ofrezcan el resto de repositorios.

El siguiente ejemplo muestra cómo instalar el software *`nagios`* desde el repositorio *EPEL*: 

```bash
sudo yum --enablerepo=epel install nagios
```

## Algunos recursos útiles

Se recomienda leer la página de manual de *yum*, donde se puede encontrar información detallada de todas sus opciones:

```bash
man yum
info yum
```
