# Tema 3 - Gestión básica de usuarios y permisos

Este tema trata sobre la gestión de usuarios y grupos de usuarios en sistemas
CentOS 7. Pero la teoría y filosofía de trabajo es aplicable a cualquier sistema
Linux. También describe quién es el usuario *root*.

## Introducción

En los sistemas Linux, todo lo que un usuario puede o no puede hacer con un
fichero está determinado por permisos y grupos. Si se dispone de los permisos 
necesarios, o si se pertenece al grupo con permisos necesarios, se podrá
acceder a un fichero o directorio.

### Repaso rápido a los permisos de ficheros y directorios

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
__________     ____  _____                      ______
     |            |     |                         |
     |            |     |                         -> Nombre
     |            |     |
     |            |     -> Grupo con permisos sobre el fichero
     |            |
     |            -> Usuario propietario del fichero
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
```

A modo de recordatorio, la siguiente tabla muestra la equivalencia entre valores
expresados en base 2 (binarios) y valores expresados en base 8 (octal):

| Octal  | Binario    |
| :----- | :--------: |
|  0     |  0 0 0     |
|  1     |  0 0 1     |
|  2     |  0 1 0     |
|  3     |  0 1 1     |
|  4     |  1 0 0     |
|  5     |  1 0 1     |
|  6     |  1 1 0     |
|  7     |  1 1 1     |

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
  - **w**: Se pueden crear o eliminar ficheros contenidos en el directorio.
  - **x**: Se puede acceder al directorio, y a sus subdirectorios (hacer un *cd*).

Hay que tener en cuenta que, cuando no se tiene permiso de lectura de un 
directorio, no se pueden mostrar (listar) sus contenidos, pero sí se puede, por
ejemplo, leer un fichero que esté dentro: tendríamos que usar la ruta absoluta
para leer un fichero dentro de un diretcorio que no podemos listar. Esto implica
que se debe conocer a priori el nombre completo del contenido de un directorio.

En Linux los tipos de ficheros son:

| Código   | Tipo de objeto                        |
| :------- | :-----------------------------------: |
|  -       |  Fichero regular.                     |
|  d       |  Directorio.                          |
|  l       |  Enlace simbólico.                    |
|  c       |  Dispositivo (especial) de carácter.  |
|  b       |  Dispositivo (especial) de bloque.    |
|  p       |  FIFO.                                |
|  s       |  Socket.                              |



### Propietarios de un fichero o un directorio

Un fichero pertenece a un **usuario** y a un **grupo**. Los usuarios y los 
grupos tienen asociados un identificador numérico, llamados *Id. de usuario*
(**UID**) e *Id. de grupo* (**GID**).

Los usuarios del sistema están definidos en los ficheros `/etc/passwd` y 
`/etc/shadow`.

Los grupos están definidos en los ficheros `/etc/group` y 
`/etc/gshadow`.

```bash
cat /etc/group | grep fabi

# El usuario fabi aparece en dos líneas:
fabi:x:1000:fabi
vboxsf:x:990:fabi

```

En el ejemplo anterior, puedo ver que el usuario *fabi* tiene *UID = 1000* y 
*GID = 1000*. El usuario *vboxsf* tiene *UID = 990* y *GID = 990*.

## Usuarios: /etc/passwd y /etc/shadow

Antes de nada, como usuario de un sistema Linux: ¿quién soy yo? ¿a qué grupos
pertenezco? Ambas preguntas se responden con dos comandos:

```bash
# ¿Qué usuario soy yo?
whoami

# ¿A qué grupos pertenezco?
groups

# Obteniendo toda la información a la vez
id


man id
```

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
2. Contraseña cifrada (o una x que indica que se usa */etc/shadow*)
3. UID (código numérico del usuario)
4. GID (código numérico del grupo) - Aunque un usuario puede pertenecer a más de
   un grupo.
5. Comentarios: por ejemplo el nombre completo, o el nombre de la empresa, etc.
6. Directorio *home* (ruta absoluta): normalmente */home/$USER*.
7. *Shell* del usuario cuando hace *login*: por ejemplo, */bin/bash*.

Normalmente, el fichero */etc/passwd* puede ser leido por todos los usuarios, 
pero sólo el usuario *root* puede modificar su contenido.

Si el usuario como contraseña posee una **x**, quiere decir que su contraseña
cifrada se encuentra almacenada en el fichero `/etc/shadow`. Este fichero sólo
puede ser leído y modificado por *root*.

No obstante, un usuario puede modificar su contraseña mediante el comando
**passwd**:

```bash
passwd


man passwd
man shadow
```

## Algunos recursos útiles

- [Permissions - Ryans Tutorials](https://ryanstutorials.net/linuxtutorial/permissions.php)
- [Permissions - IBM Developer](https://developer.ibm.com/tutorials/l-lpic1-104-5/)