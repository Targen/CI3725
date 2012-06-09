>   La definicion del lenguaje para la instruccion repeticion indeterminada es asi:
>
>       while <Bool> repeat <Instr> done
>
> pero ese Bool en realidad es una expresion que luego debe ser booleano, pero que no lo podemos saber en esta etapa cierto? Por lo que dijiste en uno de los correos que me mandaste, no habria manera de saber el tipo de lo que esté en la guardia con certeza de manera general. Explico por que lo digo: nosotros agrupamos todas las expresiones aritmeticas, booleanas, sobre lienzos en una sola regla asi:
>
>       EXPRESION : EXPRESION TkSuma EXPRESION
>                 | EXPRESION TkMenor EXPRESION
>                 | EXPRESION TkDisyuncion EXPRESION
>                 | EXPRESION TkHorConcat EXPRESION
>                 ...
>
>   para poder representar todo como una expresion binaria. Y al final tenemos
>
>       EXPRESION : TkNum | TkIdent | TkTrue | TkFalse | TkLienzo.
>
>   Por lo tanto construimos una regla tipo:
>
>       repIndet : TkWhile expresion TkRepeat instruccion TkDone
>
>  no podemos hacer
>
>       repIndet : TkWhile TkTrue(o false) TkRepeat instruccion TkDone
>
> porque eso forzaria a que la guardia tenga ya un valor booleano, y no podriamos hacer comparaciones como 1 > 2 en la guardia. Creo que como lo tenemos esta bien pero cualquier observacion que tengas es bienvenida jaja.

Precisamente: en la condición de una estructura de control condicionada puede ocurrir cualquier expresión cuyo tipo sea booleano, y como solo es posible hacer el cálculo y la validación de tipos de manera completa si se cuenta con tablas de símbolos (que no son para esta etapa del proyecto), no es necesario que verifiquen eso, y pueden aceptar cualquier expresión en las condiciones.

Como lo tienen está bien.



- - -



> Un programa puede ser vacio? Por ejemplo se puede reconocer un archivo en blanco como programa valido, sin nada de caracteres?

No.  La sección 0 de la especificación de AsGArD dice:

>   Un programa en AsGArD tiene la siguiente estructura:
>
>       [ using 〈Lista de Declaraciones〉 ] begin
>           〈Instrucción〉
>       end

Esto implica que la palabra vacía no es un programa válido en AsGArD, porque no tiene `begin`, `end` ni una instrucción entre los dos.



- - -



>   Y es obligatorio que despues de la ultima instruccion no haya punto y coma? Porque nosotros partimos de una regla:
>
>       instruccion : instruccion TkPuntoYComa instruccion
>
>   y de ahi construimos las instrucciones. Pero en caso de que se permitan
> programas en blanco necesitaremos cambiarlo a:
>
>       instruccion : instruccion TkPuntoYComa instruccion | vacio
>
>   pero eso sgnificaria que pueden haber programas como: `x := 5 ;` porque podriamos tener que la ultima instruccion es vacia

En efecto, las instrucciones no pueden ser vacías; todas sus formas posibles se describen en la sección 1 de la especificación de AsGArD y ninguna de las formas enumeradas puede ser vacía; además, el punto y coma es un *separador* de instrucciones, como en GaCeLa, mas **no** un *terminador* de instrucciones, como en C, por lo que, por ejemplo, el siguiente texto **no** es un programa válido en AsGArD:

    begin x := 5 ; end



- - -



> La secuenciacion debe ser asociativa correcto? Para poder permitir cosas como `x := 8 ; c := 7 ; d := true`.

En efecto.  Es equivalente que la representen en su gramática como una operación binaria que combina instrucciones en una sola, o como que siempre que pueda ocurri una instrucción en la gramática, en realidad puede ocurrir una *lista* de instrucciones *separadas* por un punto y coma,  La segunda estructura producirá árboles de menor algura que se visualizan con más facilidad.

Si lo hacen como un operador de combinación de instrucciones, que también es perfectamente válido, tendrían que especificar una asociatividad para eliminar un conflicto *shift/reduce*; es equivalente hacerla izquierda o derecha, pero el *parser* tendrá mayor eficiencia de ejecución si la asociatividad es izquierda (por razones que se estudiarán más adelante en el curso).
