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

## Variables (I)

Además de las variables de entorno, se pueden definir y usar variables internas en un *script* (variables de *shell*).

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

### Sustitución de comandos (command-substitution)

Sirve para ejecutar un comando *in-situ* y utilizar su salida. Para realizar una sustitución de comandos, se usa la construcción **$( )**.

```bash
echo "Estamos a $( date )."

CURR_DIR=$( pwd )
ls -la ${CURR_DIR}

```

### Exportar variables (visibilidad, ámbito o scope)

Una variable declarada dentro de un *script* sólo es visible por ese *script*: es una **variable de _shell_**. Si se desea que los hijos de ese *script* tengan acceso a las variables, hay que exportarlas. Cuando se exporta una variable de *shell*, se convierte en una **variable de entorno**, que es visible desde los procesos hijos.

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

Además de exportar una variable, también puede eliminarse (su contenido pasa a estar indefinido, *null*). Para ello se usa el comando `unset`.

```bash
export myVar='Andrómeda'
echo "myVar = $myVar"
unset myVar
echo "myVar = $myVar"

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
|  **       |  Potencia                            |

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

## Expresiones booleanas (test)

Para evaluar expresiones booleanas en *Bash*, se usa el comando `test`. El comando *test* compara dos expresiones mediante un operador.

```bash

# Comprueba si la variable $num es igual a 1
num=1
test $num -eq 1
echo "El resultado de 'test \$num -eq 1' es: $?"

num=2
test $num -eq 1
echo "El resultado de 'test \$num -eq 1' es: $?"


man test
```

`IMPORTANTE`: cuando se usan variables con *test*, deben inicializarse primero. Si se usa *test* con una variable sin inicializar, se puede producir un error.

Todas la operaciones que puede hacer *test*, se pueden usar en estructuras de control (como *if*, o como los bucles). Para ello se suele usar la notación `[ ]` o `[[ ]]`, que se mostrará más adelante.

En la página del manual de *test* se especifican todas los posibles operadores que acepta *test*. Se muestran a continuación algunos de los más comunes:

| Operador               | Operación (Compara ...)                                     |
| :--------------------- | :---------------------------------------------------------: |
|  ! EXPRESION           |  la EXPRESION es falsa (NOT).                               |
|  -n STRING             |  la longitud de STRING es mayor que 0.                      |
|  -z STRING             |  la longitud de STRING es 0 (está vacío).                   |
|  STRING1 = STRING2     |  el contenido de STRING1 es igual al de STRING2.            |
|  STRING1 != STRING2    |  el contenido de STRING1 es distinto al de STRING2.         |
|  INTEGER1 -eq INTEGER2 |  INTEGER1 es igual a INTEGER2 (valor numérico).             |
|  INTEGER1 -ne INTEGER2 |  INTEGER1 es distinto a INTEGER2 (valor numérico).          |
|  INTEGER1 -gt INTEGER2 |  INTEGER1 es mayor que INTEGER2 (valor numérico).           |
|  INTEGER1 -ge INTEGER2 |  INTEGER1 es mayor o igual que INTEGER2 (valor numérico).   |
|  INTEGER1 -lt INTEGER2 |  INTEGER1 es menor que INTEGER2 (valor numérico).           |
|  INTEGER1 -le INTEGER2 |  INTEGER1 es menor o igual que INTEGER2 (valor numérico).   |
|  -e /path/to/file      |  existe '/path/to/file'.                                    |
|  -f /path/to/file      |  existe '/path/to/file' y es un fichero regular.            |
|  -d /path/to/file      |  existe '/path/to/file' y es un directorio.                 |
|  -h /path/to/file      |  existe '/path/to/file' y es un enlace simbólico.           |
|  -r /path/to/file      |  existe '/path/to/file' y puede ser leído.                  |
|  -w /path/to/file      |  existe '/path/to/file' y puede ser escrito.                |
|  -x /path/to/file      |  existe '/path/to/file' y puede ser ejecutado.              |
|  -s /path/to/file      |  existe '/path/to/file' y no está vacío (tamaño > 0 bytes). |
|  -O /path/to/file      |  existe '/path/to/file' y soy su propietario.               |

### -eq vs. =

Conviene destacar que *-eq* se comporta de forma diferente a *=*, como muestra el siguiente ejemplo:

```bash
test 007 -eq 7
echo "El resultado de 'test 007 -eq 7' es: $?"

test 007 = 7
echo "El resultado de 'test 007 -eq 7' es: $?"

```

Como puede apreciarse, *=* realiza una comparación de *strings*, mientras que *-eq* realiza una comparación numérica.

## Estructuras condicionales

### IF

Se usa una estructura `if` para evaluar una expresión booleana.

También se utiliza para evaluar la salida de un comando.

```bash
if grep -s "pull request" ./README.md; then
    echo ">>> grep OK"
else
    echo "<<< grep KO"
fi

```

Los siguientes ejemplos muestran cómo usar el comando `test` (`[ ]`) con una estructura *if*.

```bash
num=5

if [ $num -gt 20 ]
then
    echo "Wow, el número $num es mayor que 20!! :D"
elif [ $num -gt 10 ]
then
    echo "Bien, el número $num es mayor que 10 :)"
else
    echo "Vaya, el número $num es menor o igual que 10 :("
fi

```

El ejemplo anterior también destaca la importancia del espaciado. Por una parte, hay un espacio entre la palabra *if* o *elif* y el correspondiente corchete de apertura *[*. También hay espacios entre los corchetes (*[ ]*) y su contenido (por ejemplo *$num -gt 10*). 

Además, para una mayor claridad, se han indentado los bloques de código del *if* y del *else*. 

En lugar de poner *if* y *then* en líneas distintas, se pueden poner en la misma línea usando un ';'

```bash
num=5

if [ $num -gt 20 ]; then
    echo "Wow, el número $num es mayor que 20!! :D"
elif [ $num -gt 10 ]; then
    echo "Bien, el número $num es mayor que 10 :)"
else
    echo "Vaya, el número $num es menor o igual que 10 :("
fi

```

### Operaciones booleanas

Son aplicables a todas las estructuras de control. Los siguientes ejemplos muestran su uso con *if*, pero son extrapolables a cualquier estructura de control.

```bash
# AND: &&
num=12

if [ $num -gt 10 ] && [ $num -le 20 ]
then
    echo "El número $num es mayor que 10, y menor o igual que 20 :)"
else
    echo "Vaya, el número $num es menor o igual que 10, o mayor que 20 :("
fi

# OR: ||
nombre='Perry'
apellido='Meison'

if [ "$nombre" = 'Perry' ] || [ "$apellido" = 'Foster' ]
then
    echo 'O bien el nombre es Perry, o bien el apellido es Foster'
else
    echo 'Ni el nombre es Perry, ni el apellido es Foster'
fi

# NOT: !
file='/etc/tortilla'

if [ ! -f "$file" ]
then
    echo "Vale, el fichero '$file' no existe..."
else
    echo "¡Suerte! Existe el fichero '$file'."
fi

```

Existe una forma alternativa de referirse a los operadores *&&* (AND) y *||* (OR).

```bash
# AND: -a
num=12

if [ $num -gt 10 -a $num -le 20 ]
then
    echo "El número $num es mayor que 10, y menor o igual que 20 :)"
else
    echo "Vaya, el número $num es menor o igual que 10, o mayor que 20 :("
fi

# OR: -o
nombre='Perry'
apellido='Meison'

if [ "$nombre" = 'Perry' -o "$apellido" = 'Foster' ]
then
    echo 'O bien el nombre es Perry, o bien el apellido es Foster'
else
    echo 'Ni el nombre es Perry, ni el apellido es Foster'
fi

```

### CASE

La estructura `case` se usa para comparar una variable con una serie de patrones. Su comportamiento también puede realizarse con *if*, pero a veces un *case* es más elegante. Veamos un ejemplo típico:

```bash
cd
cat <<'CASE' >case.sh
#!/bin/bash

# File: case.sh

case "$1" in
    'start')
        echo 'start...'
        ;;
    'stop')
        echo 'stop...'
        ;;
    'restart')
        echo 'restart...'
        ;;
    *)
        echo 'huh?'
        ;;
esac

CASE

chmod a+x case.sh
./case.sh

```

El contenido del fichero `case.h` es:

```bash
#!/bin/bash

# File: case.sh

case "$1" in
    'start')
        echo 'start...'
        ;;
    'stop')
        echo 'stop...'
        ;;
    'restart')
        echo 'restart...'
        ;;
    *)
        echo 'huh?'
    ;;
esac

```

## Bucles

### FOR

El bucle `for` es un poco diferente a otros lenguajes. En *Bash* se usa para iterar sobre un conjunto de *strings*. Es decir, itera sobre cada palabra de una frase.

```bash
# Itera sobre los nombres de fichero recogidos con 'ls'
for i in $( ls ~ ); do
    echo "Item: $i"
done

# Itera sobre el conjunto de números dado
for i in 1 2 3 4 5
do
    echo "Item: $i"
done

# Itera sobre los números 1 al 10
for i in $( seq 1 10 );
do
    echo "Num: $i"
done

# Aunque se prefiere la notación $(), la sustitución de comandos también puede
# emplear las comillas invertidas ( ` ` ) (back-ticks)
for i in `seq 1 10`;
do
    echo "Num: $i"
done

# Itera sobre un RANGO
for i in {1..5}
do
    echo "Num: $i"
done

# Itera sobre un RANGO, con un PASO
for i in {1..10..2}
do
    echo "Num: $i"
done

# También se puede iterar sobre elementos de distinto tipo:
for i in tortilla 7 * 8 cebolla 
do
    echo "Item: $i"
done

# Como se acaba de ver en el bloque de código anterior, un bucle for es muy
# apropiado para recorrer el contenido de un directorio
for i in * 
do
    echo "Item: $i"
done

for i in /etc/* 
do
    echo "Item: $i"
done

# Podemos probar más cosas:
for i in tortilla 7 "*" 8 cebolla 
do
    echo "Item: $i"
done

for i in tortilla 7 \* 8 cebolla 
do
    echo "Item: $i"
done

for i in tortilla 7 8 "cows are going mad"
do
    echo "Item: $i"
done

for d in poc dev qa pre prod; do
    local today="$( date '+%d-%m-%Y' )"
    local now="$( date '+%T' )"
    cat <<CONFIG >config-${d}.json
{
    "deployment": {
        "environment": "${d}",
        "date": "${today}",
        "hour": "${now}",
        "team": "Data Engineering",
        "servers": {
            "bastion": {
                "ip": "192.168.1.100",
                "port": 22333,
                "proto": "ssh"
            },
            "front": {
                "ip": "${d}.web-guay.com",
                "port": 443,
                "proto": "https"
            },
            "db": {
                "ip": "192.168.10.80",
                "port": 3306,
                "proto": "mariadb"
            }
        }
    }
}
CONFIG

done

ls -l

# También se puede usar el formato del lenguaje C:
for (( k=0; k<=7; k++ )); do
    echo "k = ${k}"
done

```

### WHILE

El bucle `while` ejecuta un bloque de código mientras se cumpla una condición (expresión de control). Termina su ejecución cuando la condición deja de ser cierta (o cuando se ejecuta *break*).

```bash
COUNTER=0
while [ $COUNTER -lt 10 ]; do
    echo "El contador es: $COUNTER"
    (( COUNTER++ )) 
done
```

Se puede hacer un *while* infinito de varias formas. A continuación se muestra una típica:


```bash
cat <<'INF' >infinite_while.sh
#!/bin/sh

# File: infinite_while.sh

while :
do
    echo 'Dime algo, o pulsa CTRL + C para salir:'
    read STR
    echo ">>> $STR"
done

INF

chmod a+x infinite_while.sh
./infinite_while.sh

```

El contenido de *infinite_while.sh* es:

```bash
#!/bin/sh

# File: infinite_while.sh

while :
do
    echo 'Dime algo, o pulsa CTRL + C para salir:'
    read STR
    echo ">>> $STR"
done

```

También es común usar un bucle *while* para leer (y procesar) cada línea de un fichero. Para ello también se usa *read* y redirecciones:

```bash
cat <<'RECORDSCOUNT' >records_count.sh
#!/bin/sh

# File: records_count.sh

cat <<'RECORDS' >records.csv
His Hero is Gone;1996;Fifteen Counts of Arson;Prank Records
His Hero is Gone;1997;Monuments to Thieves;Prank Records
His Hero is Gone;1998;The Plot Sickens;The Great American Steak Religion
Tragedy;2000;Tragedy;Tragedy Records
Tragedy;2001;Tragedy;Skuld Releases
Tragedy;2002;Can We Call This Life?;Tragedy Records
Tragedy;2002;Vengeance:Tragedy Records
Tragedy;2003;Vengeance:Skuld Releases
Tragedy;2003;Split 7" with Totalitär;Armageddon Label
Tragedy;2004;UK 2004 Tour EP;Tragedy Records
Tragedy;2006;Nerve Damage;Tragedy Records
Tragedy;2012;Darker Days Ahead;Tragedy Records
Tragedy;2018;Fury;Tragedy Records
RECORDS

HHIG=0
TGDY=0
while read f; do
    band="$( echo "${f}" | cut -d';' -f1 )"
    case ${band} in
        'His Hero is Gone')
            (( HHIG++ ))
            echo '>>> HHIG + 1'
        ;;
        'Tragedy')
            (( TGDY++ ))
            echo '>>> TGDY + 1'
        ;;
        *)
            echo '>>> Banda desconocida... ($f)'
        ;;
    esac
done < records.csv

echo ">>> Total HHIG = ${HHIG}"
echo ">>> Total TGDY = ${TGDY}"

RECORDSCOUNT

chmod a+x records_count.sh
./records_count.sh

```

El contenido de *records_count.sh* sería:

```bash
#!/bin/sh

# File: records_count.sh

cat <<'RECORDS' >records.csv
His Hero is Gone;1996;Fifteen Counts of Arson;Prank Records
His Hero is Gone;1997;Monuments to Thieves;Prank Records
His Hero is Gone;1998;The Plot Sickens;The Great American Steak Religion
Tragedy;2000;Tragedy;Tragedy Records
Tragedy;2001;Tragedy;Skuld Releases
Tragedy;2002;Can We Call This Life?;Tragedy Records
Tragedy;2002;Vengeance:Tragedy Records
Tragedy;2003;Vengeance:Skuld Releases
Tragedy;2003;Split 7" with Totalitär;Armageddon Label
Tragedy;2004;UK 2004 Tour EP;Tragedy Records
Tragedy;2006;Nerve Damage;Tragedy Records
Tragedy;2012;Darker Days Ahead;Tragedy Records
Tragedy;2018;Fury;Tragedy Records
RECORDS

HHIG=0
TGDY=0
while read f; do
    band="$( echo "${f}" | cut -d';' -f1 )"
    case ${band} in
        'His Hero is Gone')
            (( HHIG++ ))
            echo '>>> HHIG + 1'
        ;;
        'Tragedy')
            (( TGDY++ ))
            echo '>>> TGDY + 1'
        ;;
        *)
            echo '>>> Banda desconocida... ($f)'
        ;;
    esac
done < records.csv

echo ">>> Total HHIG = ${HHIG}"
echo ">>> Total TGDY = ${TGDY}"

```

### UNTIL

El bucle `until` es muy similar al bucle *while*, pero *until* se ejecuta mientras la condición es falsa, y termina su ejecución cuando la condición se vuelve verdadera.

```bash
COUNTER=20
until [ $COUNTER -lt 10 ]; do
    echo "La variable COUNTER = $COUNTER"
    let COUNTER-=1
done

```

### BREAK

Como en otros lenguajes, en *Bash* la sentencia `break` interrumpe la ejecución de un bucle, ya sea *for*, *while* o *until*

```bash
for i in {1..10..2}
do
    if [ ${i} -gt 5 ]; then
        break
    fi
    echo "Num: ${i}"
done

echo 'Hecho!'
```

### CONTINUE

*Bash* también incorpora una sentencia *continue*, que dentro de un bucle, deja de ejecutar la iteración actual y pasa a la siguiente iteración.

```bash
for i in {1..10..2}
do
    if [ ${i} -eq 5 ]; then
        continue
    fi
    echo "Num: ${i}"
done

echo 'Hecho!'
```

## Entrada a un script

Además de los argumentos de entrada ya vistos anteriormente, o de las señales que se le pueden enviar (*signals*), un *script* puede tomar su entrada de otras fuentes:

- El usuario introduce datos manualmente al *script* (desde la línea de comandos).
- La salida de otro comando o programa se usa como entrada al *script*.

### Comando read

Se usa `read` cuando se quiere que el usuario introduzca algún valor, típicamente por teclado.

```bash
cd
cat <<'READUSER' >read_user.sh
#!/bin/bash

# File: read_user.sh

echo "Hola, quién eres?"
read NOMBRE
echo "Encantada de conocerte, $NOMBRE"
READUSER

chmod a+x read_user.sh
./read_user.sh

```

El contenido del fichero *read_user.sh* sería:

```bash
#!/bin/bash

# File: read_user.sh

echo "Hola, quién eres?"
read NOMBRE
echo "Encantada de conocerte, $NOMBRE"
```

*read* tiene algunas opciones interesantes, como **-p** o **-s**:

```bash
#!/bin/bash

# File: login.sh

read -p 'Username: ' LOGIN_USERNAME
read -sp 'Password: ' LOGIN_PASSWORD
echo
echo "Gracias $LOGIN_USERNAME, ahora puedes acceder al sistema"
echo "Aunque no hayas visto la contraseña, está aquí: $LOGIN_PASSWORD"

```

Con *read* se pueden leer varias variables a la vez:

```bash
#!/bin/bash

# File: planets.sh

echo 'Dime tres planetas: '
read planet1 planet2 planet3
echo "Primero visitaremos ${planet1}, para enterarnos de qué va esto."
echo "Si necesitamos seguridad, podemos pasar por ${planet2}."
echo "Nos reuniremos con el resto del equipo en ${planet3}."
```

La siguiente tabla muestra algunas de la opciones más comunes de *read*:

| Opción                 | Descripción                                                 |
| :--------------------- | :---------------------------------------------------------: |
|  -a ARR_NAME           |  Las palabras son asignadas en orden a los índices del *array* ARR_NAME (ojo, sobreescribe ARR_NAME). |
|  -d DELIM              |  Se usa el carácter DELIM para finalizar la operación de *read* (por defecto *read* termina cuando se pulsa *ENTER*, es decir, con */n*). |
|  -n NUMCHARS           |  El comando *read* finaliza después de haber leído NUMCHARS caracteres. |
|  -p PROMPT             |  Muestra el mensaje PROMPT delante de la entrada de texto. |
|  -s                    |  Modo silencioso: no se muestra el texto que se escribe (útil con contraseñas). |
|  -t TIMEOUT            |  Hace que *read* termine y devuelva un error, si no se completa la entrada antes de TIMEOUT segundos. |
|  -u FD                 |  Lee desde el descriptor de fichero FD. |

En la página de manual de *read* se pueden ver todas las posibilidades que ofrece.

```bash
man read
```

También se puede usar en bucles, para hacer programas con *prompt*:

```bash
#!/bin/sh

# File: repl.sh

STR='vamos'
while [ "$STR" != 'q' ]; do
    echo 'Dime algo, o tecela q para salir:'
    read STR
    echo ">>> $STR"
done

```

### Leer desde STDIN

Cuando un *script* lee desde *STDIN*, se permite que otros comandos puedan enviarle su salida (usando un *pipe* o '|').

Como ya se ha explicado, *Bash* usa unos (descriptores) ficheros especiales. Cada proceso tiene sus ficheros *STDIN*, *STDOUT* y *STDERR*, y pueden ser relacionados con los ficheros *STDIN*, *STDOUT* y *STDERR* de otro proceso. Esto ocurre cuando usamos un *pipe* (|) o una redirección. Así, cada proceso tiene su propio conjunto de ficheros especiales:

- STDIN  - /proc/<PID>/fd/0
- STDOUT - /proc/<PID>/fd/1
- STDERR - /proc/<PID>/fd/2

Para simplificar el acceso a estos ficheros dede los procesos, cada proceso puede usar estos atajos:

- STDIN  - /dev/stdin  o /proc/self/fd/0
- STDOUT - /dev/stdout o /proc/self/fd/1
- STDERR - /dev/stderr o /proc/self/fd/2

Así que si un *script* necesita leer datos a través de un *pipe*, todo lo que debe hacer es leer el fichero correspondiente. Aunque estos ficheros son algo especiales, se comportan como cualquier otro fichero en Linux.

```bash
#!/bin/bash

cat <<'RECORDS' >records.csv
His Hero is Gone;1996;Fifteen Counts of Arson;Prank Records
His Hero is Gone;1997;Monuments to Thieves;Prank Records
His Hero is Gone;1998;The Plot Sickens;The Great American Steak Religion
Tragedy;2000;Tragedy;Tragedy Records
Tragedy;2001;Tragedy;Skuld Releases
Tragedy;2002;Can We Call This Life?;Tragedy Records
Tragedy;2002;Vengeance:Tragedy Records
Tragedy;2003;Vengeance:Skuld Releases
Tragedy;2003;Split 7" with Totalitär;Armageddon Label
Tragedy;2004;UK 2004 Tour EP;Tragedy Records
Tragedy;2006;Nerve Damage;Tragedy Records
Tragedy;2012;Darker Days Ahead;Tragedy Records
Tragedy;2018;Fury;Tragedy Records
RECORDS

cat <<'CSV' >filter_csv.sh
#!/bin/bash

# File: filter_csv.sh
echo '>>> Discos:'
echo
cat /dev/stdin | cut -d';' -f 2,3 | sort
CSV

chmod a+x filter_csv.sh
cat records.csv | ./filter_csv.sh

```

## Funciones

Como en otros lenguajes de programación, en *Bash* se pueden declarar funciones para agrupar bloques de código. Veamos un ejemplo sencillo:

```bash
#!/bin/bash

saluda() {
    echo "Hola, terrícola"
}

saluda

```

### Valor de retorno de una función

Como el resto de comandos en *Bash*, una función devuelve un número entero indicando el estado de salida. Típicamente un valor de retorno de 0 implica una ejecución correcta. Un valor mayor que 0, implica que hubo errores durante la ejecución.

Para devolver un valor se usa la sentencia `return`.

```bash
#!/bin/bash

saluda() {
    echo "Hola, terrícola"
    return 42
}

saluda
echo ">>> saluda() ha devuelto: $?"

saluda_mas() {
    echo "Hola terrícola, es un placer recibirte a bordo de nuestra nave"
}

saluda_mas
echo ">>> saluda2() ha devuelto: $?"

```

En realidad, aunque una función sólo devuelva un entero, puede producir salidas de varias formas:

- Puede modificar variables externas.
- Puede invocar el comando *exit* para terminar el *script*.
- Hacer *echo* de algún texto, que puede ser almacenado usando sustitución de comandos: `miVar=$( miFuncion )`

`VARIABLES`: Como se puede ver en los ejemplos siguientes, cuando se declara una variable en un script, su **ámbito es global**, y puede ser leída o modificada dentro de una función. Se pueden declarar variables de **ámbito local**, que sólo existen dentro de una función, con la expresión **local**.

```bash
#!/bin/bash

# Define osname()
function osname() {
    local os="$( uname -s )"
    local ver="$( uname -r )"
    echo "${os} ${ver}"
    return 17
}

# Invoca a osname()
osname
echo ">>> osname() ha devuelto: $?"

n=$( osname )
echo ">>> n = ${n}"

##-------------------------------------

saluda() {
    echo "Hola, terrícola"
}

saluda
echo ">>> saluda() ha devuelto: $?"

s=$( saluda )
echo ">>> s = ${s}"

##-------------------------------------

# Ámbito de variables

num=10
val=5
echo -e ">>> num = ${num}\tval = ${val}\t\t[1]"

function varDemo() {
    local num=42
    echo -e "varDemo(): num = ${num}\tval = ${val}\t[2]"
    num=51
    val=7
}

echo -e ">>> num = ${num}\tval = ${val}\t\t[3]"

varDemo
echo ">>> varDemo() ha devuelto: $?"
echo -e ">>> num = ${num}\tval = ${val}\t\t[4]"

v=$( varDemo )
echo ">>> v = ${v}"

##-------------------------------------

function varDemo_other() {
    alpha=42
    let beta=24
    echo -e "varDemo(): alpha = ${alpha}\tbeta = ${beta}\t[1]"
}

echo -e ">>> alpha = ${alpha}\tbeta = ${beta}\t[2]"

```

### Paso de parámetros por referencia

Las versiones modernas de *Bash* incorporan una característica muy útil, que permite modificar, dentro de una función, variables externas a la función. Esta característica se denomina *namerefs*.

```bash
function setHost() {
    local -n ref=$1
    ref='centos.org'
}

THE_HOST='localhost'
echo $THE_HOST # -> old
setHost THE_HOST
echo $THE_HOST # -> new

```

## Variables (II)

Como se ha visto, las variables, por defecto, tienen ámbito global y no tienen un tipo específico. Pero mediante el uso de expresiones como **local**, se puede restringir su ámbito. Pero también hay formas de restringir su contenido, o si se prefiere, su tipo.

### declare

Se puede especificar el tipo de valor que puede almacenar una variable con la expresión `declare`, como se muestra a continuación:

| Opción  | Descripción                                                        |
| :-------| :----------------------------------------------------------------: |
|  -a     |  La variable es un *array*.                                        |
|  -f     |  La variable sólo contiene nombres de funciones.                   |
|  -i     |  La variable será tratada como un número entero.                   |
|  -p     |  Muestra los atributos y valores de cada variable.                 |
|  -r     |  La variable es de sólo lectura (constante).                       |
|  -x     |  Marca la variable para ser exportada.                             |

```bash
#!/bin/bash

declare -i NUM=42

echo ">>> NUM = ${NUM}"
NUM='tortilla'
echo ">>> NUM = ${NUM}"

declare -p NUM

```

### readonly

Otra forma de especificar que una variable no se puede modificar, es marcarla como de sólo lectura.

```bash
#!/bin/bash

readonly NUM=42

echo ">>> NUM = ${NUM}"
NUM=56
echo ">>> NUM = ${NUM}"

```

### Arrays

En *Bash*, como en otros lenguajes, los *arrays* son colecciones de elementos. En *Bash* los elementos pueden ser de diferentes tipos. A continuación se muestran algunas operaciones típicas con *arrays*:

```bash
#!/bin/bash

# 3 formas de crear un array:
miArr[7]=21
declare -a colores
planetas=('Júpiter', 'Urano', 'Venus')

# Asignar un valor a una posición del array:
miArr[7]=33

# Acceder a los elementos de un array:
echo "miArr[@] = ${miArr[@]}"
echo "miArr[\*] = ${miArr[*]}"
echo 'miArr[*] = '$miArr[*]

echo "miArr[7] = ${miArr[7]}"

echo '-----------------------'

echo "planetas[*] = ${planetas[*]}"
echo "planetas[2] = ${planetas[2]}"
planetas[3]='Saturno'

echo '-----------------------'

# Mostrar los índices
echo "Contenido:   planetas[*] = ${planetas[*]}"
echo "Índices:    !planetas[*] = ${!planetas[*]}"

planetas[9]='Neptuno'
echo "Contenido:   planetas[*] = ${planetas[*]}"
echo "Índices:    !planetas[*] = ${!planetas[*]}"

# Tamaño de un array (número de elementos):
echo "El array 'planetas' tiene: ${#planetas[*]} elementos."

# Tamaño del contenido de una de sus posiciones:
echo "planetas[1] tiene: ${#planetas[1]} caracteres/dígitos."

# Eliminar una posición
unset planetas[2]
echo "planetas[2] = ${planetas[2]}"

# Eliminar un array
unset planetas
echo "planetas[*] = ${planetas[*]}"
```

Un *array* puede generarse a partir de la salida de un comando:

```bash
#!/bin/bash

lista=( $( ls ~ ) )
echo ${lista[*]}
echo ${#lista[*]}

```

Es común recorrer los elementos de un *array* con un bucle *for*:

```bash
#!/bin/bash
 
numeros=(1 2 3 4)

for num in ${numeros}; do
    echo -e "num = ${num}"
done

# El ejemplo anterior no funciona, hay que convertir el array en una lista, para
# poder iterar sobre sus elementos con un bulce for:

numeros=(1 2 3 4)

for num in ${numeros[@]}; do
    echo -e "num = ${num}"
done


```

## Bibliotecas de shell

En *Bash* se pueden crear bibliotecas de funciones, para agrupar cierta funcionalidad en un fichero, que pueda ser importado por otros *scripts*.

Por ejemplo, se puede crear una sencilla biblioteca para imprimir mensajes de *debug* desde nuestros scripts. A continuación se detalla el contenido del fichero `log.sh`.

```bash
#
# log.sh is a small shell library to customize log messages.
#

. colors.sh

DEBUG_MESSAGE_TYPE=(info, warning, error, debug)
DEBUG=1

#!
#!  \function       dbg()
#!  \brief          Prints a message on console, but only if DEBUG is defined.
#!
#!  \param[in]      $@: (string) Message to be printed. Normally this parameter
#!                  would be a string, but it can also be a set of strings.
#!
function dbg {
    if [ -n "${DEBUG}" ]; then
        echo -e "$@"
    fi
}

#! Prints the given message(s) as an INFO message on console.
function info {
    dbg "${IGREEN}[INFO]${NC} $@"
}

#! Prints the given message(s) as a WARNING message on console.
function warning {
    dbg "${YELLOW}[WARNING] $@ ${NC}"
}

#! Prints the given message(s) as an ERROR message on console.
function error {
    dbg "${RED}[ERROR] $@ ${NC}"
}

#! Prints the given message(s) as a DEBUG message on console.
function debug {
    dbg "${CYAN}[DEBUG] $@ ${NC}"
}

#!
#!  \function       log()
#!  \brief          Prints a debug message on console. It calls the appropriate
#!                  error log function, to print customized log messages.
#!
#!  \param[in]      $1: (string) (OPTIONAL) Debug message type. When this 
#!                  parmeter is omitted, an INFO message type is printed.
#!  \param[in]      $@: (string) Message to be printed. Normally this parameter
#!                  would be a string, but it can also be a set of strings.
#!
#!  \globals[in]    $DEBUG
#!
function log {
    local msg_type

    if [ "$#" -gt 1 ]; then
        msg_type="${1}"
        shift
    fi

    case "${msg_type}" in
        debug|DEBUG)
            debug "$@"
            ;;
        error|ERROR)
            error "$@"
            ;;
        warning|WARNING)
            warning "$@"
            ;;
        *)
            info "$@"
            ;;
    esac
}

#!
#!  \function       msg()
#!  \brief          Prints a message on console. It can print color text.
#!
#!  \param[in]      $1: (string) (OPTIONAL) Text color (see colors.sh).
#!  \param[in]      $@: (string) Message to be printed. Normally this parameter
#!                  would be a string, but it can also be a set of strings.
#!
function msg {
    local msg_color

    if [ "$#" -gt 1 ]; then
        msg_color="${1}"
        shift
    fi

    echo -e "${msg_color}$@${NC}"
}

```

Como se puede ver, utiliza un fichero llamado `colors.sh` que contiene algunas definiciones de colores:

```bash
# Small collection of Bash colors

# https://stackoverflow.com/questions/5947742/how-to-change-the-output-color-of-echo-in-linux

# Reset colors
NC='\033[0m'

# Regular Colors
BLACK='\033[0;30m'        # Black
RED='\033[0;31m'          # Red
GREEN='\033[0;32m'        # Green
YELLOW='\033[0;33m'       # Yellow
BLUE='\033[0;34m'         # Blue
PURPLE='\033[0;35m'       # Purple
CYAN='\033[0;36m'         # Cyan
WHITE='\033[0;37m'        # White

# Bold
BBLACK='\033[1;30m'       # Black
BRED='\033[1;31m'         # Red
BGREEN='\033[1;32m'       # Green
BYELLOW='\033[1;33m'      # Yellow
BBLUE='\033[1;34m'        # Blue
BPURPLE='\033[1;35m'      # Purple
BCYAN='\033[1;36m'        # Cyan
BWHITE='\033[1;37m'       # White

# Underline
UBLACK='\033[4;30m'       # Black
URED='\033[4;31m'         # Red
UGREEN='\033[4;32m'       # Green
UYELLOW='\033[4;33m'      # Yellow
UBLUE='\033[4;34m'        # Blue
UPURPLE='\033[4;35m'      # Purple
UCYAN='\033[4;36m'        # Cyan
UWHITE='\033[4;37m'       # White

# Background
BG_BLACK='\033[40m'       # Black
BG_RED='\033[41m'         # Red
BG_GREEN='\033[42m'       # Green
BG_YELLOW='\033[43m'      # Yellow
BG_BLUE='\033[44m'        # Blue
BG_PURPLE='\033[45m'      # Purple
BG_CYAN='\033[46m'        # Cyan
BG_WHITE='\033[47m'       # White

# High Intensity
IBLACK='\033[0;90m'       # Black
IRED='\033[0;91m'         # Red
IGREEN='\033[0;92m'       # Green
IYELLOW='\033[0;93m'      # Yellow
IBLUE='\033[0;94m'        # Blue
IPURPLE='\033[0;95m'      # Purple
ICYAN='\033[0;96m'        # Cyan
IWHITE='\033[0;97m'       # White

# Bold High Intensity
BIBLACK='\033[1;90m'      # Black
BIRED='\033[1;91m'        # Red
BIGREEN='\033[1;92m'      # Green
BIYELLOW='\033[1;93m'     # Yellow
BIBLUE='\033[1;94m'       # Blue
BIPURPLE='\033[1;95m'     # Purple
BICYAN='\033[1;96m'       # Cyan
BIWHITE='\033[1;97m'      # White

# High Intensity backgrounds
BG_IBLACK='\033[0;100m'   # Black
BG_IRED='\033[0;101m'     # Red
BG_IGREEN='\033[0;102m'   # Green
BG_IYELLOW='\033[0;103m'  # Yellow
BG_IBLUE='\033[0;104m'    # Blue
BG_IPURPLE='\033[0;105m'  # Purple
BG_ICYAN='\033[0;106m'    # Cyan
BG_IWHITE='\033[0;107m'   # White

```

Por último, se puede crear un *script* de prueba de la biblioteca. Por ejemplo, se muestra el contenido del fichero `testLog.sh`, que servirá para probar la funcionalidad de la biblioteca:

```bash
#!/bin/bash

. log.sh

error "Esto es un error"
warning "Esto es un warning"
info "Esto es un info"
debug "Esto es un debug"
dbg "Esto es un dbg"
echo '-------------------------'

DEBUG=
error "Este 'error' no sale"
warning "Tampoco sal el 'warning'"
info "Este 'info' tampoco sale"
debug "Este 'debug' no sale"
dbg "Este 'dbg' no debe imprimirse"

log "Esto es un log a pelo, que sale al margen del valor de DEBUG"
log info "Esto es un 'log info', que sale al margen del valor de DEBUG"
log warning "Esto es un 'log warning', que sale al margen del valor de DEBUG"
log error "Esto es un 'log error', que sale al margen del valor de DEBUG"
log debug "Esto es un 'log debug', que sale al margen del valor de DEBUG"

msg "'msg' también debe funcionar aquí"
msg ${CYAN} "'msg' también debe funcionar aquí, lo mismo pero en cyan"

echo '-------------------------'

DEBUG=1
debug "Esto vuelve a salir"
warning "Esto también vuelve a salir"
dbg "Ahora 'dbg' vuelve a funcionar"

log "Esto es un log a pelo"
log info "Esto es un 'log info'"
log warning "Esto es un 'log warning'"
log error "Esto es un 'log error'"
log debug "Esto es un 'log debug'"

```

Tras examinar este ejemplo, se puede observar que:

- Las bibliotecas en *Bash* no tienen *shebang* (el fichero no empieza con *#!/bin/bash*). Tiene sentido, pues no se desea ejecutar su código, sino usarlo desde un *script*.
- Para "importar" una biblioteca, se usa la palabra `source` o el punto (`.`). En el ejemplo, *testLog.sh* usa las funciones definidas en *log.sh*. A su vez, *log.sh* usa las variables definidas en *colors.sh*.
- Cualquier variable definida en la biblioteca, es accesible desde los *scripts* que hayan importado la bilbioteca.
- Si una biblioteca contiene código (fuera de una función), ese código será ejecutado cuando se importe la biblioteca. Hay que prestar atención a esto.

## Manejo de strings

Hay una característica de las versiones modernas de *Bash*, que permite "trocear" *strings* (cadenas de caracteres) cómodamente:

```bash
str='me gusta el rock, me gusta el punk, me gusta la música espiritual de Armenia'
pos=16
len=4

# Extrae una subcadena de la cadena $str en la posición $pos.
echo -e "${str:${pos}}"

# Extrae una subcadena de $len caracteres de la cadena $str en la posición
# $pos (los índices de un string comienzan en 0).
echo -e "${str:${pos}:${len}}"

sub='me gusta'

# Recorta la ocurrencia más corta de la subcadena $sub desde el principio
# de la cadena $str.
echo -e "${str#*${sub}}"
# ' el rock, me gusta el punk, me gusta la música espiritual de Armenia'

# Recorta la ocurrencia más larga de la subcadena $sub desde el principio
# de la cadena $str.
echo -e "${str##*${sub}}"
# '  la música espiritual de Armenia'

# Recorta la ocurrencia más corta de la subcadena $sub desde el final
# de la cadena $str.
echo -e "${str%${sub}*}"
# 'me gusta el rock, me gusta el punk, '

# Recorta la ocurrencia más larga de la subcadena $sub desde el final
# de la cadena $str.
echo -e "${str%%${sub}*}"
# ''

new='me mola'

# Sustituye la primera ocurrencia de la subcadena $sub con el contenido de $new.
echo -e "${str/${sub}/${new}}"

# Sustituye todas las ocurrencias de la subcadena $sub con el contenido de $new.
echo -e "${str//${sub}/${new}}"

# Si la subcadena $sub está al principio de la cadena $str, sustituye $sub con el
# el contenido de $new.
echo -e "${str/#${sub}/${new}}"

# Si la subcadena $sub está al final de la cadena $str, sustituye $sub con el
# el contenido de $new.
echo -e "${str/%${sub}/${new}}"

# Veamos otro ejemplo más típico:
str='mifichero.tar.gz'
echo -e "${str#*.}"     # tar.gz
echo -e "${str##*.}"    # gz
echo -e "${str%.*}"     # mifichero.tar
echo -e "${str%%.*}"    # mifichero

```


Cuando se trabaja con rutas, hay dos comandos muy útiles: `basename` y `dirname`.

```bash
basename '/home/fabi/tortilla.txt' # tortilla.txt
dirname '/home/fabi/tortilla.txt' # /home/fabi

```

## Algunos recursos útiles

- [Bash conditional expressions](https://www.gnu.org/software/bash/manual/html_node/Bash-Conditional-Expressions.html)
- [Operadores de comparación](https://www.tldp.org/LDP/abs/html/comparison-ops.html)
- [Operadores aritméticos](https://www.tldp.org/LDP/abs/html/ops.html)
- [RedHat Certified System Administration Notes](https://codingbee.net/rhcsa)
