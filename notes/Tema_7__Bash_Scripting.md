# Tema 7 - Bash Scripting

Los *scripts*, de forma similar a los programas, permiten automatizar tareas y agrupar comandos que, normalmente, se escribirían en la *shell*. De hecho, cualquier comando o expresión que se puede ejecutar en la *shell*, puede ser ejecutado en un *script*, y viceversa.

Este tema es una introducción a la programación de *scripts de shell*, usando *Bash*. El contenido es eminentemente práctico.

## ¿Cómo se ejecutan los scripts en Bash?

Básicamente, cuando se ejecuta un *script* en una *shell*, esta creará un sub-proceso, dentro del cual se ejecuta el *script*. El *script* necesitará tener permisos de ejecución.

```bash
cd

# Crea el script 'hola.sh':
cat <<SCRIPT >hola.sh
#!/bin/bash

echo "Hola, Bash"
cd
pwd
ls -la
SCRIPT

# Otorga permiso de ejecución al script:
chmod a+x hola.sh

# Ejecuta el script (se usa ./ porque el directorio actual no está en el $PATH):
./hola.sh

```

### Shebang: #!

La primera línea de un *script* hace referencia al intérprete que ejecutará el contenido del fichero (el código del *script*). Por ejemplo, para el anterior *script* `hola.sh` se compone de:

- **#!** : A estos dos caracteres en la primera línea de un *script* se les denomina **shebang**.
- **/bin/bash** : Junto al *shebang* se especifica el **intérprete** del *script* (en este caso, es **/bin/bash**).

ormalmente usaremos el intérprete */bin/bash*, pero podrían usarse diferentes intérpretes de *shell*, como: */bin/csh*, */bin/zsh*, etc.

También se puede usar un *shebang* para ejecutar código escrito en algún lenguaje de *scripting*, como Python o Ruby.

Conviene especificar siempre el *shebang* para cada script, y siempre debe estar en la primera línea, y escribirse tal cual, sin espacios entre sus partes.

## Variables

Además de las variables de entorno, se pueden definir y usar variables internas en un *script*.

```bash
# Crea la variable MSG y le asigna un valor (signo = sin espacios)
MSG='Hola caracola'

# Para usar una variable, se usa el carácter $ antes de su nombre:
echo "El mensaje es: $MSG"

# Es buena práctica rodear la variable con {}
echo "El mensaje es el mismo: ${MSG}"

# Como se ha visto, cuando se usan comillas dobles ("), se sustituye la variable
# por su contenido (para eso se usa el símbolo $).

# Cuando se usan comillas simples ('), no hay susitución de variables, ya que 
# las comillas simples construyen textos literales:
echo 'El mensaje es: $MSG'

# Los nombres de las variables distinguen mayúsculas y minúsculas:
msg='A message to you, Rudy'
msG='Mrs. Gullible'
echo "msg = ${msg}"
echo "msG = ${msG}"

# Se puede usar la sustitución de comandos para almacenar la salida de un 
# comando en una variable:
CURR_DIR=$( pwd )
echo "El directorio actual es: ${CURR_DIR}"

# El resultado es el mismo si la sustitución de comandos se rodea con comillas
# dobles:
CURR_DIR="$( pwd )"
echo "El directorio actual sigue siendo: ${CURR_DIR}"

```

### Argumentos de invocación de un script

```bash
cd

cat <<SCRIPT >args.sh
#!/bin/bash

echo "El nombre de este scrpit es: $0"
echo "El primer argumento es: $1"
echo "El segundo argumento es: $2"
echo "El número de argumentos es: $#"
echo "Todos los argumentos son: $@"

# Puedo hacer copias de los argumentos:
FIRST_ARG=$1
echo "FIRST_ARG = ${FIRST_ARG}"
SCRIPT

chmod a+x args.sh

./args.sh

```



 También se pueden exportar las variables internas

## Algunos recursos útiles

