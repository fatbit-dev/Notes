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
msg='A message to you, Trudy'
msG='Mrs. Gullible'
echo "msg = ${msg}"
echo "msG = ${msG}"

# Se puede usar la sustitución de comandos para almacenar la salida de un 
# comando en una variable (command-substitution):
CURR_DIR=$( pwd )
echo "El directorio actual es: ${CURR_DIR}"

# El resultado es el mismo si la sustitución de comandos se rodea con comillas
# dobles:
CURR_DIR="$( pwd )"
echo "El directorio actual sigue siendo: ${CURR_DIR}"

# Un poco más acerca de las comillas dobles y simples:
echo "Aquí hay 'comillas simples' dentro de comillas dobles..."
echo 'Aquí hay "comillas dobles" dentro de comillas simples...'
echo 'Aquí hay '\''comillas simples'\'' dentro de comillas simples (concatenación y escape)...'
echo "Aquí hay \"comillas dobles\" dentro de comillas dobles (escape)..."

```

### Argumentos de invocación de un script

```bash
cd

cat <<'SCRIPT' >args.sh
#!/bin/bash

echo "-----------------------------------"
echo "El nombre de este script es: $0"
echo "El primer argumento es: $1"
echo "El segundo argumento es: $2"
echo "El número de argumentos es: $#"
echo "Todos los argumentos son: $@"

# Puedo hacer copias de los argumentos:
FIRST_ARG=$1
echo "FIRST_ARG = ${FIRST_ARG}"
echo
SCRIPT

chmod a+x args.sh

./args.sh
./args.sh "Esto es Bash!"
./args.sh "¿Cuál es la respuesta?" 42
./args.sh "Tortilla" "Cebolla" "Patata" 5

```

Un detalle curioso acerca de este *script*, es que usa un *here-document* y el campo de terminación aparece entre comillas simples ('). Esto se hace para evitar que se sustituyan las variables (como *$1*, *$2*, ...) por su contenido en el momento de generar el fichero *args.sh*.

El contenido del fichero *args.sh* será:

```bash
#!/bin/bash

echo "-----------------------------------"
echo "El nombre de este script es: $0"
echo "El primer argumento es: $1"
echo "El segundo argumento es: $2"
echo "El número de argumentos es: $#"
echo "Todos los argumentos son: $@"

# Puedo hacer copias de los argumentos:
FIRST_ARG=$1
echo "FIRST_ARG = ${FIRST_ARG}"
echo
```

### Substitución de comandos (commnad-substitution)

Sirve para ejecutar un comando *in-situ* y utilizar su salida. Para realizar una sustitución de coandos, se usa la construcción **$( )**.

```bash
echo "Estamos a $( date )."

CURR_DIR=$( pwd )
ls -la ${CURR_DIR}

```

### Exportar variables (visibilidad, ámbito o scope)

Una variable declarada dentro de un *script* sólo es visible por ese *script*. Si se desea que los hijos de ese *script* tengan acceso a las variables, hay que exportarlas.

Por ejemplo, un *script* (*s1.sh*) llama a otro *script* (*s2.sh*), y se desea que una variable declarada dentro de *sh1.sh* sea visible desde *sh2.sh*: hay que **exportar** la variable desde *sh1.sh*.

```bash
cat <<'SH1' >sh1.sh
#!/bin/bash

# File: sh1.sh

var1='Galaxia'
var2=42

export CURR_DIR=$( pwd )

cd "${CURR_DIR}"

echo "[${0}]: var1 = ${var1} \t var2 = ${var2}"
export var1
./sh2.sh
echo "[${0}]: var1 = ${var1} \t var2 = ${var2}"

SH1

cat <<'SH2' >sh2.sh
#!/bin/bash

# File: sh2.sh

cd "${CURR_DIR}"

echo "[${0}]: var1 = ${var1} \t var2 = ${var2}"

var1='Constelación'
export var1

SH2

chmod a+x sh1.sh
chmod a+x sh2.sh

./sh1.sh

```

El contenido de los ficheros *sh1.sh* y *sh2.sh* sería:

- **sh1.sh**:

```bash
#!/bin/bash

# File: sh1.sh

var1='Galaxia'
var2=42
export var1

export CURR_DIR=$( pwd )

cd "${CURR_DIR}"

echo "[${0}]: var1 = ${var1} \t var2 = ${var2}"
./sh2.sh
echo "[${0}]: var1 = ${var1} \t var2 = ${var2}"

```

- **sh2.sh**:

```bash
#!/bin/bash

# File: sh2.sh

cd "${CURR_DIR}"

echo "[${0}]: var1 = ${var1} \t var2 = ${var2}"

var1='Constelación'
export var1

```

### Longitud de una variable

```bash
cat <<'LEN' >len.sh
#!/bin/bash

# File: len.sh

msg='Tortilla de patatas'
echo "La longitud de '${msg}' es: ${#msg}"

num=7251
echo "La longitud del número '${num}' es: ${#num}"

LEN

chmod a+x len.sh
./len.sh

```

El contenido del fichero *len.sh* es:

```bash
#!/bin/bash

# File: len.sh

msg='Tortilla de patatas'
echo "La longitud de '${msg}' es: ${#msg}"

num=7251
echo "La longitud del número '${num}' es: ${#num}"

```

### Algunas variables de entorno útiles

```bash
cat <<'ENVVARS' >env_vars_demo.sh
#!/bin/bash

# File: env_vars_demo.sh

cd
echo "Accediendo al directorio: $( pwd ) ..."
echo "   Status = $?"

cd /root
echo "Accediendo al directorio: /root ..."
echo "   Status = $?"

echo "Este script tiene el PID: $$"
echo "Este script ha sido ejecutado por: $USER"
echo "Este script se ejecuta en el host: $HOSTNAME"

sleep 2
echo "Este script lleva ejecutándose ${SECONDS} segundos"

# La variable RANDOM genera un número pseudo-aleatorio entre 0 y 32767.
echo "Un número aleatorio: ${RANDOM}"
echo "Otro número aleatorio: ${RANDOM}"
echo "Y otro número aleatorio más: ${RANDOM}"

echo "Esta es la línea $LINENO del script."

ENVVARS

chmod a+x env_vars_demo.sh
./env_vars_demo.sh

```

El contenido del fichero *env_vars_demo.sh* sería:

```bash
#!/bin/bash

# File: env_vars_demo.sh

cd
echo "Accediendo al directorio: $( pwd ) ..."
echo "   Status = $?"

cd /root
echo "Accediendo al directorio: /root ..."
echo "   Status = $?"

echo "Este script tiene el PID: $$"
echo "Este script ha sido ejecutado por: $USER"
echo "Este script se ejecuta en el host: $HOSTNAME"

sleep 2
echo "Este script lleva ejecutándose ${SECONDS} segundos"

# La variable RANDOM genera un número pseudo-aleatorio entre 0 y 32767.
echo "Un número aleatorio: ${RANDOM}"
echo "Otro número aleatorio: ${RANDOM}"
echo "Y otro número aleatorio más: ${RANDOM}"

echo "Esta es la línea $LINENO del script."

```

## Operaciones aritméticas

*Bash* ofrece varias formas de realizar operaciones aritméticas sencillas (como **_let_**, **_expr_** o expansión con dobles paréntesis). Algunas de las operaciones matemáticas más comunes son:

| Operador  | Operación                            |
| :-------- | :----------------------------------: |
|  +        |  Suma                                |
|  -        |  Resta                               |
|  *        |  Multiplicación                      |
|  /        |  División                            |
|  %        |  Resto o Módulo                      |
|  ++       |  Pre/Post-incremento (incrementa 1)  |
|  --       |  Pre/Post-decremento (decrementa 1)  |
|  **       |  Exponenciación                      |

A continuación se realiza un ejemplo con expansión, por considerarse una buena práctica. Se describen varias operaciones matemáticas sencillas. Para usar la sustitución, se rodea la expresión matemática con dobles paréntesis, precedidos del símbolo *$*: **\$(( ))**.

```bash
cd
cat <<'MATH' >math_ops.sh
#!/bin/bash

# File: math_ops.sh

# Se usa sustitución: con dobles paréntesis que rodean a una expresión matemática
a=$((3+4))
echo "a = ${a}"

# Es buena práctica usar espacios para mejorar la claridad y la legibilidad
a=$(( 40 + 2 ))
echo "Ahora, a = ${a}"

# Se pueden usar variables como operandos de las expresiones matemáticas.
b=$(( $a - 10 ))
echo "b = ${b}"
# Se puede omitir el símbolo $:
b=$(( 10 + a + 10 ))
echo "Ahora, b = ${b}"

# Se puede usar ++ para incrementar en 1
(( b++ ))
echo "Autoincremento: b = ${b}"

# Como en otros lenguajes, se puede sumar y asignar en una sentencia
(( b += 5 ))
echo "Suma y asigna: b = ${b}"

# Operaciones comunes
a=$(( 5 + 7 ))
echo "5 + 7 = ${a}"

a=$(( 5 - 7 ))
echo "5 - 7 = ${a}"

a=$(( 5 * 7 ))
echo "5 * 7 = ${a}"

a=$(( 5 / 7 ))
echo "5 / 7 = ${a}"
a=$(( 7 / 5 ))
echo "7 / 5 = ${a}"

a=$(( 7 % 5 ))
echo "7 % 5 = ${a}"

a=$(( 5 ** 7 ))
echo "5 ** 7 = ${a}"

MATH

chmod a+x math_ops.sh
./math_ops.sh

```

El contenido del fichero *math_ops.sh* es:

```bash
#!/bin/bash

# File: math_ops.sh

# Se usa sustitución: con dobles paréntesis que rodean a una expresión matemática
a=$((3+4))
echo "a = ${a}"

# Es buena práctica usar espacios para mejorar la claridad y la legibilidad
a=$(( 40 + 2 ))
echo "Ahora, a = ${a}"

# Se pueden usar variables como operandos de las expresiones matemáticas.
b=$(( $a - 10 ))
echo "b = ${b}"
# Se puede omitir el símbolo $:
b=$(( 10 + a + 10 ))
echo "Ahora, b = ${b}"

# Se puede usar ++ para incrementar en 1
(( b++ ))
echo "Autoincremento: b = ${b}"

# Como en otros lenguajes, se puede sumar y asignar en una sentencia
(( b += 5 ))
echo "Suma y asigna: b = ${b}"

# Operaciones comunes
a=$(( 5 + 7 ))
echo "5 + 7 = ${a}"

a=$(( 5 - 7 ))
echo "5 - 7 = ${a}"

a=$(( 5 * 7 ))
echo "5 * 7 = ${a}"

a=$(( 5 / 7 ))
echo "5 / 7 = ${a}"
a=$(( 7 / 5 ))
echo "7 / 5 = ${a}"

a=$(( 7 % 5 ))
echo "7 % 5 = ${a}"

a=$(( 5 ** 7 ))
echo "5 ** 7 = ${a}"

```




## Algunos recursos útiles

