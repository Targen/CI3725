>   Buenos dias Manuel, disculpa la molestia, me surgieron unas dudas mientras hacia la gramatica para el proyecto, espero puedas ayudarme:
>
>   Inicialmente escribi las siguientes reglas (en PYTHON):
>
>       def p_expresion(p):
>           '''expresion : expresionArit
>                        | expresionBool
>                        | expresionCanvas'''
>
>       def p_expresionArit(p):
>           '''expresionArit : expresionBinariaArit
>                            | TkResta expresionArit %prec RESTAU
>                            | TkNum
>                            | TkIdent
>                            | TkParAbre expresionArit TkParCierra'''
>
>       def p_expresionBinariaArit(p):
>           '''expresionBinariaArit : expresionArit TkSuma expresionArit
>                                   | expresionArit TkResta expresionArit
>                                   | expresionArit TkDiv expresionArit
>                                   | expresionArit TkMult expresionArit
>                                   | expresionArit TkMod expresionArit'''
>
>   Fijate que en `expresionBinariaArit` hago que las reglas dependan de `expresionArit`, esto con la finalidad de evitar cosas como `a + b /\ true` (o por lo menos lo hice con esa intencion) lo que si NO podria evitar es que en `a + b`, uno de los operandos sea de tipo booleano, pero me imagino que a este punto se refieren en el enunciado cuando dicen que para esta entrega no podemos determinar los tipos de variables.
>
>   La duda es la siguiente: ¿Vale la pena realmente intentar salvar cosas como `a + b /\ true` ? Lo que sucede es que cuando intento imprimir, el arbol con las reglas asi construidas me esta obligando a tener muchas condiciones y hacen el codigo un poco enredado.

El problema general es que el análisis que deben hacer sobre el programa, que a estas alturas es una secuencia de *tokens*, implica varios procesos que podrían estar relacionados de maneras arbitrarias, pero en el caso particular de analizar lenguajes de programación, hay algunos patrones de diseño que permiten descomponer el problema en partes que pueden hacerse por separado.

Por una parte, tienen que computar la estructura sintáctica que corresponde a la secuencia de *tokens* del programa que están analizando, porque el significado del programa está asociado a esa estructura, y el objetivo final —hacer un interpretador— requiere ese significado.  Por esto es que se construye el árbol sintáctico abstracto.

Por otra parte, hay partes del significado del programa que no necesariamente son de naturaleza sintáctica o estructural: el ejemplo más obvio en este contexto es el sistema de tipos de un lenguaje de programación.  Los sistemas de tipos varían mucho y son probablemente la parte más importante de un lenguaje de programación.

En algunos casos particulares de lenguajes muy simples (o muy controvertidos) es posible que los *cómputos* asociados al sistema de tipos (usualmente, calcular el tipo de cada variable o expresión) puedan hacerse directamente como parte del análisis estructural que es capaz de hacer un sistema basado en gramáticas libres de contexto.  En estos casos, las **reglas** del sistema de tipos del lenguaje se podrían expresar *directamente* en la gramática usando técnicas como la que describes: la regla

>   `A + B` es una expresión aritmética (léase: su tipo es entero o en general numérico) si `A` y `B` son expresiones aritméticas

se podría, quizás, expresar en la gramática como

>   ExpresiónAritmética → ExpresiónAritmética + ExpresiónAritmética

o algo similar, y eso no permitiría que sean reconocidas expresiones con tipos incorrectos, porque se encontrarían errores de sintaxis durante el proceso de análisis sintáctico que hace el *parser* construido a través de la gramática.

El problema es que AsGArD no es un lenguaje en el que esto sea completamente viable.  La ocurrencia de variables en una expresión no viene acompañada de nada que permita conocer su tipo, así que esa información —que fue dada en otro lugar, al definir la variable— no está disponible y no puede usarse para el proceso de análisis sintáctico que se estudia en este curso y que está implantado en las herramientas que están usando para el proyecto.  Las razones específicas se verán eventualmente en la teoría cuando se estudien lenguajes que no son libres de contexto.

Por esto, es **imposible** hacer la totalidad del cálculo de tipos de AsGArD como parte del proceso de análisis sintáctico con un *parser* LALR(1) (que es lo que generan sus herramientas para la segunda etapa del proyecto a partir de su gramática).  Aunque es posible hacer *parte* del cálculo a tiempo de análisis sintáctico, no es posible hacerlo *todo*, así que no vale la pena hacer *nada* de ese cálculo en ese momento y resulta más conveniente y eficiente hacerlo todo **después**, trabajando sobre el AST que representa el código con su estructura.  Esto es lo primero que tendrán que hacer para la tercera etapa del proyecto.

La consecuencia práctica de esto es que no tiene sentido, en el caso de este lenguaje, escribir una gramática que distinga entre tipos de expresiones.  El tipo de una expresión debería ser un miembro de los objetos de su AST que se calcule luego de haber construido el árbol completo, y en ese momento es que deberán detectar si hay errores en el programa por incompatibilidad de tipos.



- - -



> Por otro lado cuando hago casos de prueba que involucren conjuncion y disyuncion tengo un problema ya que solo lo reconozco si coloco los caracteres escapados, es decir (\\/ y /\\)  entonces, no sabria si esto es problema del lexer o propio de python...

En términos abstractos o matemáticos, la expresión regular

    \/

reconoce exactamente lo que dice: un *backslash*, y un *slash* en ese orden.  En la codificación usada para escribir las expresiones regulares en Python, el *backslash* tiene un significado especial: escapar caracteres especiales.  Y, como siempre *un* caracter escapador real se codifica con *dos* caracteres escapadores consecutivos.  Esa expresión regular en Python debe ser una cadena de caracteres cuyo **contenido** sea

    \\/

Además, los literales de cadena de caracteres (los que se escriben directamente entre comillas) también usan al *backslash* como caracter escapador, así que para escribir un literal de caracteres **normal** en Python que contenga a esos **tres** caracteres habría que escribir algo como

    '\\\\/'

En efecto, prueben esto en Python:

    >>> len('\\\\/')
    3

Sin embargo, hay una manera especial de escribir los literales de cadenas de caracteres en Python: los *raw strings* son literales de cadenas de caracteres donde el contenido de la cadena de caracteres es **exactamente** lo que está escrito en el código entre comillas sin escapar ninguna cosa, **excepto** la secuencia especial

    \'

que también se interpreta como los dos caracteres, pero la comilla no indica el final del literal de caracteres sino que es un caracter más de la cadena (al igual que el *backslash*) dentro de la cadena de caracteres.  Es decir: no se escapa nada, solo comillas, en cuyo caso el escapador se preserva dentro del *string* tanto como el propio caracter escapado.  Para la cadena que interesa, se podría escribir algo así:

    r'\\/'

Ese literal de cadena de caracteres tiene una ‘r’ pegada a su inicio, así que es un *raw string* que contiene a los tres caracteres que hacen falta para codificar lo que interesa.

La expresión regular que se codifica así reconoce dos caracteres, y si Python imprime una representación textual de esa cadena de caracteres, probablemente imprima

    "\\/"

aunque en realidad la cadena es de longitud 2, no 3, porque tiene un solo *backslash*.

En el código que escriban como caso de prueba, claro, ese operador solo debe tener un *backslash* y luego un *slash*.  Probablemente tienen un problema en el *lexer* si necesitan poner el *backslash* doble.
