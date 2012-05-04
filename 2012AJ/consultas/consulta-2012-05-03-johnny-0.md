> -   La primera es como se le va a dar el programa escrito en AsGArD al analizador lexicográ fico, por lo que entendí vamos a ser un script, me imagino que uno muy parecido a los que se hacen en sistemas de operación I, que después de ser llamado, nos pedirá que introduzcamos linea por linea en la consola el código en AsGArD. Es asi? o vamos a leer el codigo de un archivo?

Ni lo uno ni lo otro.  En la sección “Entrega de la Implementación” del enunciado de la etapa 1 del proyecto dice esto:

> El analizador deberá ser ejecutado con el comando “./LexAsgard”, por lo que es posible que tenga que incorporar un script a su entrega que permita que la llamada a su programa se realice de esta forma.  Note que la entrada para su programa será a través de la entrada estándar del sistema de operación.  Sin embargo, deben abstenerse de imprimir nada más que lo pedido y tener cuidado con que la salida del programa corresponda con fidelidad a los requisitos mencionados anteriormente.

Cualquier compilador de C++ es capaz de producir ejecutables nativos, así que luego de compilar su proyecto (con el comando `make`, claro, así que haría falta un Makefile) se podría ejecutar `./LexAsgard` porque ese sería el nombre del ejecutable generado.  Esencialmente lo mismo aplica para Haskell, aunque el proceso de compilación de Haskell podría hacerse con herramientas mucho mejores que Make.

Ruby y Python son básicamente lenguajes interpretados (al menos en sus implantaciones canónicas, que son las recomendadas), y el mejor, más fácil y más común mecanismo para hacer que un *script* en cualquiera de los dos lenguajes pueda ejecutarse directamente es usar el mecanismo del *shebang*.

Cuando le indicas al sistema de operación que quieres ejecutar un archivo (con permisos de ejecución, claro), primero se revisa si el archivo es un ejecutable como los que produce un compilador (y si lo es, se ejecuta).  Si el formato del archivo *no* es un ejecutable binario, entonces el sistema de operación analiza el contenido del archivo como texto: si su **primera** línea es de la forma

    #!/camino/a/un/comando argumento1 argumento2 argumento3

entonces lo que se ejecuta en realidad es el ejecutable mencionado en esa línea, que se denomina *shebang*, con los argumentos que aparecen en esa línea (y podría haber cualquier cantidad de argumentos, incluso ninguno, hasta el límite del sistema de operación que es muy alto) y un argumento adicional: el nombre del archivo que intenta ejecutarse y cuya primera línea es ese *shebang*.

La idea es que un archivo de código de un lenguaje interpretado especifique así en su primera línea cuál es el interpretador que debe usarse para ejecutarlo.  Así, las siguientes líneas contiguas podrían ser el contenido de un archivo que se ejecute con este mecanismo usando el *shell* Bash:

    #!/bin/bash
    for i in {1..10}
    do
            echo "Hola $i"
    done

Esto funciona porque el ejecutable en `/bin/bash` recibe como parámetro el camino al archivo cuyo contenido se mostró, y cuando `bash` recibe el nombre de un archivo, lo que hace es ejecutar su contenido como *shell script*; es decir, lo interpreta.

Para Python y Ruby el asunto es igual: los ejecutables `python` y `ruby` ejecutan el código contenido en el archivo cuyo nombre les sea pasado como parámetro.  El único problema es saber dónde están instalados esos ejecutables; la solución es usar a otro programa que determina esto automáticamente:

    #!/usr/bin/env ruby

para Ruby, o

    #!/usr/bin/env python

para Python, y sí, también sirve

    #!/usr/bin/env bash

para Bash.  Usando este mecanismo, el propio ejecutable es capaz de ejecutarse directamente como lo pide el enunciado sin necesidad de un *script* aparte.

El único problema que queda es Java (como de costumbre).  Los ejecutables de Java en la implantación recomendada no son texto plano (así que no se puede usar el mecanismo de *shebang*) ni código compilado para la máquina real (así que tampoco pueden ejecutarse directamente), sino código compilado para la máquina virtual de Java.  Para ejecutarlos habría que escribir algo como `java LexAsgard`.  Este es el único caso para el cual haría falta hacer un *script*, pero sería trivial:

    #!/bin/sh
    java LexAsgard

Otro detalle importante es que la entrada se lee por la entrada estándar sin ningún tipo de *prompt*.  La lectura “línea por línea” es algo que solo tiene sentido hacer cuando se está haciendo un interpretador de línea de comando, como un *shell* o un ambiente *REPL* (***r**ead, **e**val, **p**rint **l**oop*) para algún lenguaje.  Este lenguaje no funcionará con un *REPL* y el espacio en blanco no tiene relevancia sintáctica, así que **el concepto de *línea* es totalmente irrelevante**.  Su programa debería leer el archivo de entrada completo, o ir leyéndolo mientras vaya siendo necesario procesar más entrada, pero las fronteras entre una línea y otra son irrelevantes y se ignorarán como se ignora cualquier otro espacio en blanco según la especificación del lenguaje.

Como el enunciado especifica el formato de salida de la primera etapa del proyecto, no deberían producir mensajes de ningún tipo pidiendo al usuario que escriba el código a analizar, ni nada por el estilo.



- - -



> -   La otra duda que tenia es sobre esta parte del enunciado "Es importante notar que cada token producido por su analizador lexicográ co debe corresponder a un tipo de datos y no simplemente ser impreso a la salida estándar."; no entendi que quieren decir con eso, significa que tenemos que guardar los tokens en alguna parte después de que el analizador termine?

La idea es que no solamente deben imprimir por pantalla en el formato especificado cuáles son los *token*s encontrados (si no hubo errores), sino que deberían tener a los *token*s representados como entes independientes en su programa según sea adecuado en su lenguaje.  En Haskell deben hacer un tipo abstracto de datos (con `data`); en C++ lo ideal sería una tupla o un registro con un valor de un `enum class` y el dato contenido si lo hay; en Ruby querrán definir una clase para cada *token* (y pueden aprovechar técnicas de metaprogramación para hacerlo con mucha facilidad); en el caso de Python con PLY, que no trabaja con tipos distintos para cada *token* sino con reflexión y variables con nombres especiales, ese requerimiento tiene poco sentido, pero podrían arreglárselas con una simple tupla.

El problema con ese requerimiento del enunciado es que no puede ser demasiado específico por la diversidad de lenguajes que pueden usar para el proyecto.  En general la idea es que no se limiten a simplemente imprimir los *token*s cuando los encuentren, sino que tengan alguna estructura de datos independiente que los represente y almacene.
