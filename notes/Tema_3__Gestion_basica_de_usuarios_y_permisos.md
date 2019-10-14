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

### Usuarios: /etc/passwd y /etc/shadow

Cuando se crea un usuario en el sistema, se almacena la siguiente información
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
