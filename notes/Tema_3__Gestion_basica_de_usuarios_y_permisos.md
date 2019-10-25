# Tema 3 - Gestión básica de usuarios y permisos

Este tema trata sobre la gestión de usuarios y grupos de usuarios en sistemas
CentOS 7. Pero la teoría y filosofía de trabajo es aplicable a cualquier sistema
Linux. También describe quién es el usuario *root*.

En los sistemas Linux, todo lo que un usuario puede o no puede hacer con un
fichero está determinado por permisos y grupos. Si se dispone de los permisos 
necesarios, o si se pertenece al grupo con permisos necesarios, se podrá
acceder a un fichero o directorio.

## Permisos de ficheros y directorios

```bash
ls -l /etc/passwd
-rw-r--r--. 1 root root 1395 sep 22 01:14 /etc/passwd

ls -ld /etc/
drwxr-xr-x. 102 root root 8192 oct 13 05:21 /etc/

```

Los listados anteriores muestran información sobre el propietario y los permisos
de los ficheros. Hagamos un repaso:

```text

drwxr-xr-x 102 root  root    8192 oct 13 05:21  /etc/
__________     ____  _____                      
     |           |     |                           
     |           |     -> Grupo con permisos sobre el fichero                           
     |           |     
     |           -> Usuario propietario del fichero
     |           
     |
     -> Permisos del fichero:
       - Tipo de fichero (regular, directorio, enlace, etc).
       - Permisos del usuario/propietario ( r w x )
       - Permisos del grupo ( r - x )
       - Permisos para el resto de usuarios ( r - x )

```

Así que los permisos se agrupan para Usuario, Grupo y Otros:

```text

-  r w x     r - x     r - x
   _____     _____     _____
     |         |         |
     |         |          -> Permisos para Otros usuarios
     |         |          
     |         -> Permisos para el Grupo propietario
     |
     -> Permisos para el Usuario propietario
```

Normalmente, estos permisos se representan como 3 números en base octal. Para el
ejemplo anterior, los permisos serían 755:

```text
-  r w x     r - x     r - x
   1 1 1     1 0 1     1 0 1
   \___/     \___/     \___/
     7         5         5

Otro ejemplo:

-  r w -     r - -     r - -
   1 1 0     1 0 0     1 0 0
   \___/     \___/     \___/
     6         4         4
```

A modo de recordatorio, la siguiente tabla muestra la equivalencia entre valores
expresados en base 2 (binarios) y valores expresados en base 8 (octal):

| Octal  | Binario   | Permisos   |
| :----- | :-------: | :--------: |
|  0     |  0 0 0    |  - - -     |
|  1     |  0 0 1    |  - - x     |
|  2     |  0 1 0    |  - w -     |
|  3     |  0 1 1    |  - w x     |
|  4     |  1 0 0    |  r - -     |
|  5     |  1 0 1    |  r - x     |
|  6     |  1 1 0    |  r w -     |
|  7     |  1 1 1    |  r w x     |

Hay una pequeña diferencia entre el significado de los permisos para ficheros y
directorios:

- **Ficheros**
  - **r**: El fichero puede ser leído (se lee su contenido).
  - **w**: El fichero puede ser escrito/modificado (se escribe/modifica su 
    contenido).
  - **x**: El fichero puede ser ejecutado (el fichero es un programa o *script*).

- **Directorios**
  - **r**: Se puede listar el contenido del directorio (hacer un *ls*, por 
    ejemplo).
  - **w**: Se pueden crear o eliminar ficheros dentro del directorio.
  - **x**: Se puede acceder al directorio, y a sus subdirectorios (hacer un *cd*).

Hay que tener en cuenta que, cuando no se tiene permiso de lectura de un 
directorio, no se pueden mostrar (listar) sus contenidos, pero sí se puede, por
ejemplo, leer un fichero que esté dentro: tendríamos que usar la ruta absoluta
para leer un fichero dentro de un diretcorio que no podemos listar. Esto implica
que se debe conocer a priori el nombre completo del contenido de un directorio.

Además, aunque no se pueda leer (**r**) o escribir (**w**) en un directorio, 
siempre que se pueda acceder a él (**x**), se podrá trabajar con los ficheros
que contiene. Dicho de otra manera, los permisos de un directorio no son los
permisos de los ficheros (y sub-directorios) que contiene.

Recordatorio: al hacer un `ls -l` podemos ver de qué tipo es un fichero:

| Código   | Tipo de objeto                        |
| :------- | :-----------------------------------: |
|  -       |  Fichero regular.                     |
|  d       |  Directorio.                          |
|  l       |  Enlace simbólico.                    |
|  c       |  Dispositivo (especial) de carácter.  |
|  b       |  Dispositivo (especial) de bloque.    |
|  p       |  FIFO.                                |
|  s       |  Socket.                              |

### Cambiar los permisos de ficheros y directorios

Se usa la herramienta **`chmod`** (*change file mode bits*) para cambiar los
permisos. Los argumentos con los que se invoca a *chmod* son: 

  - ¿Para quién se cambian los permisos?: usuario, grupo, otros, todos. [ugoa].
  - ¿Se conceden permisos [+] o se revocan permisos [-]?
  - ¿Qué permiso se configura?: lectura, escritura y/o ejecución [rwx]

A continuación se muestran algunos ejemplos de permisos para ficheros:

```bash
cd
mkdir permisos
cd permisos
echo "Este es un fichero para jugar con chmod" > permisos.txt

# Vamos a cambiar algunos permisos

ls -l
# -rw-rw-r--. 1 fabi fabi 40 oct 14 21:15 permisos.txt

chmod o-r permisos.txt
ls -l
# -rw-rw----. 1 fabi fabi 40 oct 14 21:15 permisos.txt

chmod g+x permisos.txt
ls -l
# -rw-rwx---. 1 fabi fabi 40 oct 14 21:15 permisos.txt

chmod u-r permisos.txt
ls -l
# --w-rwx---. 1 fabi fabi 40 oct 14 21:15 permisos.txt

# También podemos cambiar varios permisos a la vez

chmod u+rx permisos.txt
ls -l
# -rwxrwx---. 1 fabi fabi 40 oct 14 21:15 permisos.txt

chmod go-wx permisos.txt
ls -l
# -rwxr-----. 1 fabi fabi 40 oct 14 21:15 permisos.txt

chmod a-x permisos.txt
ls -l
# -rw-r-----. 1 fabi fabi 40 oct 14 21:15 permisos.txt

chmod a+rw permisos.txt
ls -l
# -rw-rw-rw-. 1 fabi fabi 40 oct 14 21:15 permisos.txt

```

También es común usar los valores expresados en base octal para cambiar los
los permisos:

```bash
chmod 755 permisos.txt
ls -l
# -rwxr-xr-x. 1 fabi fabi 40 oct 14 21:15 permisos.txt

chmod 750 permisos.txt
ls -l
# -rwxr-x---. 1 fabi fabi 40 oct 14 21:15 permisos.txt

chmod 644 permisos.txt
ls -l
# -rw-r--r--. 1 fabi fabi 40 oct 14 21:15 permisos.txt

chmod 650 permisos.txt
ls -l
# -rw-r-x---. 1 fabi fabi 40 oct 14 21:15 permisos.txt

chmod 666 permisos.txt
ls -l
# -rw-rw-rw-. 1 fabi fabi 40 oct 14 21:15 permisos.txt

```

Y para comprender mejor cómo funcionan los permisos en los directorios, veamos
algunos ejemplos:

```bash
cd
ls -l permisos
# -rwxr-xr-x. 1 fabi fabi 40 oct 14 21:15 permisos.txt

ls -ld permisos
# drwxrwxr-x. 2 fabi fabi 26 oct 14 21:15 permisos/

chmod 400 permisos
ls -ld permisos
# dr--------. 2 fabi fabi 26 oct 14 21:15 permisos/

cd permisos/
# bash: cd: permisos/: Permiso denegado

ls permisos/
# ls: no se puede acceder a permisos/permisos.txt: Permiso denegado
# permisos.txt

chmod 100 permisos
ls -ld permisos
# d--x------. 2 fabi fabi 26 oct 14 21:15 permisos/

ls permisos
# ls: no se puede abrir el directorio permisos/: Permiso denegado

cd permisos
ls
# ls: no se puede abrir el directorio .: Permiso denegado

pwd
#/home/fabi/permisos

cat permisos.txt
# Este es un fichero para jugar con chmod


man chmod
```

### Propietarios de un fichero o un directorio

Un fichero pertenece a un **usuario** y a un **grupo**. Los usuarios y los 
grupos tienen asociados un identificador numérico, llamados *Id. de usuario*
(**UID**) e *Id. de grupo* (**GID**).

Los usuarios del sistema están definidos en los ficheros `/etc/passwd` y 
`/etc/shadow`.

Los grupos están definidos en los ficheros `/etc/group` y `/etc/gshadow`.

```bash
cat /etc/group | grep fabi

# El usuario fabi aparece en dos líneas:
fabi:x:1000:fabi
vboxsf:x:990:fabi

```

En el ejemplo anterior, se puede ver que el usuario *fabi* tiene *UID = 1000* y 
*GID = 1000*. El usuario *vboxsf* tiene *UID = 990* y *GID = 990*.

También se puede ver que el usuario *fabi* pertenece al grupo *vboxsf*.

### Cambiar el propietario de un fichero o directorio

```bash
su -

# Cambia el usuario y el grupo de un fichero o directorio
chown fabi:developers /home/fabi/license.txt
chown fabi:developers /home/fabi/projects/

# Cambia el usuario y el grupo de un directorio y todo su contenido
chown -R fabi:developers /home/fabi/projects
```

## Usuarios

Antes de nada, como usuario de un sistema Linux: ¿quién soy yo? ¿a qué grupos
pertenezco? Ambas preguntas se responden con estos comandos:

```bash
# ¿Qué usuario soy yo?
whoami

# ¿A qué grupos pertenezco?
groups

# Obteniendo toda la información a la vez
id

id -u

id -n

id -g


man id
```

### /etc/passwd y /etc/shadow

Cuando se crea un usuario en el sistema, se alamcena la siguiente información
en el fichero `/etc/passwd`. Esta información son 7 campos, separados por 
caracteres (`:`). 

```bash
username:x:UID:GID:comment:home_directory:login_shell
    1    2  3   4     5        6               7

root:x:0:0:root:/root:/bin/bash
fabi:x:1000:1000:Fabi:/home/fabi:/bin/bash
```

Los campos son:

1. Id. de usuario (se almacena en las variables $USER o $LOGNAME de la *shell*.)
2. Contraseña cifrada (o una x que indica que se usa `/etc/shadow`)
3. UID (código numérico del usuario)
4. GID (código numérico del grupo) - Aunque un usuario puede pertenecer a más de
   un grupo.
5. Comentarios: por ejemplo el nombre completo, o el nombre de la empresa, etc.
6. Directorio *home* (ruta absoluta): normalmente */home/$USER*.
7. *Shell* del usuario cuando hace *login*: por ejemplo, */bin/bash*.

Normalmente, el fichero */etc/passwd* puede ser leido por todos los usuarios, 
pero sólo el usuario *root* puede modificar su contenido.

Si el usuario tiene una **x**, quiere decir que su contraseña cifrada se 
encuentra almacenada en el fichero */etc/shadow*. Este fichero sólo puede ser 
leído y modificado por *root*.

### passwd

Un usuario puede modificar su contraseña usando el comando `passwd`:

```bash
su -
passwd

# Para cambiar la contraseña de un usuario en particular, se ejecuta:
passwd fabi


man passwd
man shadow
```

### Añadir usuarios

Al añadir un usuario se crea su directorio personal en */home*. Por ejemplo, para
la usuaria *peppa*, se creará el directorio */home/peppa*. Dentro de */home/peppa*,
se copia una plantilla (que se encuentra en */etc/skel*).

Tras crear al usuario, hay que asignarle una constraseña.

```bash
sudo adduser peppa
sudo passwd peppa
```

Algunas de las opciones más comunes al usar estos programas son:

```bash
# Crea un usuario y establece el grupo inicial al que pertenece. (-g)
# Por ejemplo, para un grupo llamado developers se haría:
sudo adduser -g developers peppa

# Para añadir más grupos se usa el flag (-G)
sudo adduser -G grupo1,grupo2 peppa

# Para no crear un directorio de usuario en /home (-M)
sudo adduser -M peppa

# Para crear una cuenta de sistema (tampoco crea nada en /home) (-r, --system)
sudo adduser -r peppa


# Se puede forzar la acción de cambio de contraseña (-f)
sudo passwd -f peppa

# Se puede borrar la contraseña de un usuario
sudo passwd -d peppa


man adduser
man passwd
```

### Eliminar usuarios

```bash
# Elimina un usuario, pero conserva sus ficheros en /home/peppa
sudo userdel peppa

# Elimina un usuario, y elimina además el directorio /home/peppa. También se
# elimina al usuario de los grupos a los que pertenecía.
sudo userdel -r peppa


man userdel
```

### Añadir un usuario a un grupo

Normalmente, sólo un usuario con privilegios puede añadir un otro usuario a uno 
o más grupos:

```bash
su -

# Añade un usuario a dos grupos con _usermod_
usermod -aG ungrupo,otrogrupo fabi

# También se puede usar el comando _gpasswd_
gpasswd -a fabi ungrupo


man usermod
man gpasswd
```

## Grupos: /etc/gpasswd y /etc/gshadow

Un grupo es un conjunto de usuarios que comparten los mismos permisos.

Hay dos tipos de grupos:

- Primario
- Secundarios

En Linux, la información relativa a los grupos está en los ficheros 
`/etc/group` y `/etc/gshadow`.

### /etc/group

Cada grupo está definido en una línea del fichero */etc/group*. Cada línea consta
de 4 campos (separados por `:`)

```bash
cat /etc/group | grep fabi

# El usuario fabi aparece en dos líneas:
fabi:x:1000:fabi
 1   2   3   4

```

1. Nombre del grupo.
2. Contraseña cifrada (**x** indica que la contraseña se guarda en /etc/gpaswd).
3. GID.
4. Lista de miembros (separados por `,`).

### Añadir grupos

```bash
groupadd developers


man groupadd
```

### Modificar grupos

```bash
# Se puede modificar el GID
groupmod -g 1003 developers

# Se puede modificar el nombre
groupmod -n desarrolladores developers


man groupmod
```

### Eliminar grupos

Un grupo no se podrá eliminar si tiene algún miembro.

```bash
groupdel developers


man groupdel
```

## El usuario root

El usuario *root* es un super-usuario que puede hacer todo lo que quiera en el 
sistema: es el usuario con más privilegios. Esto le permite poder mantener y
administrar el sistema.

Normalmente, sólo el dueño de un fichero/directorio puede modificar los permisos
del mismo. Y normalmente un usuario sólo tiene total libertad dentro de su *home*.

Sin embargo, el usuario *root* puede modificar los permisos de cualquier fichero
del sistema, incluso aunque no sea el propietario.

De igual forma que podemos acceder al sistema con nuestro usuario, también 
podemos acceder al sistema como *root*, para tener todos los privilegios.

Si ya hemos accedido como un usuario pero queremos convertirnos en *root*, 
podemos usar el comando **su**.

```bash
su -
```

También se usa *su* para convertirnos en otro usuario existente en el sistema:

```bash
su - juanjo
```

### El comando sudo

Permite ejecutar comandos con permisos de *root* o super usuario. En CentOS 7, 
para que un usuario pueda ejecutar comandos con *sudo*, debe pertenecer al grupo
*wheel*.

Sólo un usuario con privilegios puede añadir a otro usuario al grupo de *sudoers*.
Para gestionar los usuarios que pueden usar *sudo*, se usa el comando *visudo*.
Este comando abre un editor (*vi*) para editar el fichero */etc/sudoers*.

```bash
su -

# Ya como root, ejecutamos:
visudo

```

Al usar *visudo*, se debe buscar una línea similar a la siguiente:

```text
%wheel        ALL=(ALL)       ALL
```

Si la línea estuviese comentada (comienza por #), habrá que descomentarla.

Una vez hecho esto, se añade al usuario al grupo *wheel*:

```bash
usermod -aG wheel fabi
```

Para que los cambios tengan efecto, se debe salir de la sesión y volver a
entrar, o también se puede abrir una nueva *shell* con el usuario:

```bash
su - fabi

# Ya como el usuario fabi, se puede probar:
sudo ls -laR /
```

### Otra forma alternativa de añadir un usuario a /etc/sudoers

En lugar de añadir el usuario al grupo *wheel* también se puede añadir el usuario
usando visudo, e insertando una línea similar a esta:

```text
fabi        ALL=(ALL)        ALL
```

## Para ampliar

El tema de gestión de usuarios da para varias asignaturas, pero tras esta
primera introducción, se puede buscar información acerca de **suid**, **sgid**
**sticky bit**, **ficheros inmutables**.

Mucha de la configuración relativa a usuarios y grupos se guarda en el fichero
`/etc/login.defs` :)

- `umask`

## Algunos recursos útiles

- [Permissions - Ryans Tutorials](https://ryanstutorials.net/linuxtutorial/permissions.php)
- [Permissions - IBM Developer](https://developer.ibm.com/tutorials/l-lpic1-104-5/)
