# Tema 5 - Editores de texto (vim y nano)

En este tema se ven muy brevemente los editores de texto `vim` y `nano`. Se han elegido estos dos porque son muy comunes, y suelen venir preinstalados en las distribuciones más populares (por ejemplo, *nano* es el editor por defecto en distribuciones Debian o derivadas). La idea de este tema es tener unas nociones básicas que nos permitan editar nuestros propios *scripts*.

Para instalar los editores hay que ejecutar:

```bash
yum install nano vim
```

## nano

`nano` es un sencillo editor de texto. Se puede iniciar *nano* con un nuevo fichero o se puede editar un fichero ya existente:

```bash
# Inicia nano con un nuevo fichero (inicialmente vacío)
nano

# Edita un fichero ya existente
nano ~/.bashrc


man nano
```

A continuación se especifican algunos de sus atajos de teclado (*shortcuts*) comunes:

- `Ctrl + O`: Guarda el fichero que se está editando. Si no se especificó ningún fichero a editar, preguntará por el nombre de fichero que se quiere guardar.
- `Ctrl + X`: Sale de *nano*. Cuando hay cambios sin guardar, preguntará al usuario si desea guardar los cambios en el fichero. Y si no se especificó ningún fichero, preguntará por el nombre de fichero que se quiere guardar.
- `Ctrl + K`: Corta una línea, y la copia en el portapapeles.
- `Ctrl + U`: Pega el contenido del portapapeles.
- `Ctrl + W`: Busca un texto en el documento.
- `Ctrl + G`: Muestra la ayuda básica del programa.
- `Ctrl + _`: Ir a la línea (pregunta por el número de línea).
- `Alt + U`: Deshace la última edición.

## vim

`vim` es un potente y completo editor de textos, que además permite ser ampliado mediante *plugins*. 

```bash
# Inicia vim con un nuevo fichero (inicialmente vacío)
vim

# Edita un fichero ya existente
vim ~/.bashrc


man vim
```

El manejo de *vim* es más complejo que el de *nano*. *Vim* tiene dos modos de funcionamiento:

- Modo de comandos (**ESC**).
- Modo de inserción (**i**).

Al iniciar *vim*, se accede al modo de comandos. Para empezar a editar un fichero (y pasar al modo de inserción), se pueden pulsar diferentes teclas, como por ejemplo:

- **i**: **Insert** - Entra al modo de inserción (en el punto en el que esté el cursor).
- **a**: **Append** - Entra al modo de inserción (en el carácter siguiente al punto en el que esté el cursor).
- **s**: **Supress** - Entra al modo de inserción (elimina el carácter en el que esté el cursor).

En la terminología de *vim*, lo que se ve en pantalla se denomina *buffer*. Por lo que cuando se guarda un fichero, en realidad se guarda el *buffer* en el fichero.

Desde el modo de inserción se puede editar el fichero, y cuando se quiera realizar alguna acción, se debe volver al modo de comandos (pulsando la tecla **ESC**).

Algunas de las operaciones básicas que se pueden hacer desde el modo de comandos son:

- **:w**: Guarda el fichero. Si no se especificó ningún fichero a editar, preguntará por el nombre de fichero que se quiere guardar.
- **:q**: Sale de *vim*. Cuando hay cambios sin guardar, preguntará al usuario si desea guardar los cambios en el fichero. Y si no se especificó ningún fichero, preguntará por el nombre de fichero que se quiere guardar.
- **:q!**: Sale de *vim*, forzando la salida, de tal forma que se perderán los cambios no guardados.
- **:númeroDeLínea**: Ir a la línea *númeroDeLínea*. Por ejemplo: *:4*, va a la línea número 4 del fichero, y sitúa el cursor en el primer carácter del fichero.
- **dd**: Corta la línea actual (y la copia en el portapapeles).
- **p**: Pega el contenido que está en el portapapeles, en la línea siguiente a la actual (desplaza el texto existente hacia abajo).
- **P**: Pega el contenido que está en el portapapeles, en la línea actual (desplaza el texto existente hacia abajo).
- **Y**: Copia la línea actual en el portapapeles.
- **/**: Se usa la barra */* seguida de un término de búsqueda, para buscarlo en el fichero.
- **n**: Mientras se está en una búsqueda, se desplaza a la siguiente ocurrencia del término de búsuqeda en el fichero.
- **N**: Mientras se está en una búsqueda, se desplaza a la anterior ocurrencia del término de búsuqeda en el fichero.
- **:%s/uno/otro/g**: Reemplaza el texto *uno* por el texto *otro* en todo el fichero.
- **u**: Deshace la última edición.

### .vimrc

El fichero `$HOME/.vimrc` almacena la configuración de *vim* para un usuario en particular. En este fichero se puede, por ejemplo, activar el resaltado de código, especificar a cuántos espacios se traduce un tabulador, mostrar los números de línea, etc.

Se deja aquí un ejemplo básico de fichero *.vimrc*:

```text
syntax on
set tabstop=2
set hlsearch
set ruler
set mouse=nc
set pastetoggle=<F2>
set foldmethod=marker
```

## Para ampliar

Nano es un editor sencillo, y vim es mucho más complejo, y dado que este tema es sólo una pincelada de ambas herramientas, se recomienda buscar información sobre ellos para sacarles todo el partido.

Opcionalmente, se puede investigar acerca de los (numerosísimos) *plugins* que existen para *vim*, o también buscar más información sobre cómo configurar el fichero *.vimrc*.
