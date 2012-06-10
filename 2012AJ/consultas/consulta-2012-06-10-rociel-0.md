>   La instruccion using puede aparecer mas de una vez en un programa asgard?  O solo se usa para definir las variables al principio? Osea seria como cualquier otra instruccion que se puede usar dondequiera que puede aparecer una?

Sí puede, pero en la raíz hay exactamente una.

La sección 1 de la especificación de AsGArD, en su subsección titulada “Incorporación de alcance”, especifica que una forma de construir una instrucción en AsGArD es una instrucción de incorporación de alcance y tiene exactamente la misma estructura sintáctica que el programa completo.

Según el primer párrafo de la sección 0 de la especificación, no es válido en AsGArD un programa vacío, y tampoco uno así

    begin print 27 end
    begin print 42 end

ni tampoco así

    using x of type integer begin print 27 end
    using x of type integer begin print 42 end

Eso solo es un problema en el nivel raíz, porque debe haber **exactamente** una.  Sin embargo, esa estructura sintáctica es una instrucción, así que sí se pueden anidar y secuenciar:

    begin
        begin print 27 end ;
        begin print 42 end
    end

También con el `using`:

    using x of type integer begin
        begin print 27 end ;
        using x of type integer begin print 42 end
    end



- - -



>   El caso que nos esta faltando en estos momentos es el del operador unario `-`. Nosotros tratamos de seguir el ejemplo de ply, donde sobreescriben la precedencia original del `-` para los casos `4 * -9` por ejemplo. El problema es que en nuestro caso no puede ser igual porque nosotros no evaluamos de una vez, nosotros construimos ast's que luego imprimimos. Y no estamos muy claros de como podriamos almacenar el -valor en una clase distinta a la usada para los numeros. Es decir nosotros tratamos de hacer:
>
>       'EXPRESION : TkResta EXPRESION %prec UTkResta'
>       p[0] = ast.UResta(-p[2])
>
> donde uresta es una clase que definiria un arbol para este caso. Pero no funcionara porque expresion termina recursivamente en un numero. Lo que se nos ocurrio es que podriamos tratar de construir simplemente una instancia de la clase expresion binaria como si fuera un caso `7 - 9` comun y corriente, pero en el que el hijo izquierdo seria nulo. Luego imprimiriamos como lo hacemos normalmente. Para que tengas una idea, si tenemos `4 + 9` imprimimos algo como:
>
>       - operacion: suma
>       - tipo: integer
>       - operando izquierdo: INT_LITERAL 4
>       - operando derecho INT_LITERAL 9
>
>   si implementamos los operadores unarios como te estoy explicando al imprimir algo como `-9` se veria asi:
>
>       - operacion: resta
>       - tipo: integer
>       - operando izquierdo: NINGUNO
>       - operando derecho INT_LITERAL 9
>
> Y eso lo hariamos con todos los operadores unarios. Estaria bien eso? Si la respuesta es no, podrias sugerirnos algo de como atacar el problema? Ahi estamos un poco desorientados. Gracias.

En realidad no hay ninguna restricción impuesta por el enunciado sobre cómo debe ser la representación abstracta ni sobre cómo debe ser la impresión.  Sin embargo, cuando la operación es un negativo y no una resta, deberían representarla como un negativo, y no como una resta.  Más adelante resultará más sencillo tener cada cosa representada como lo que realmente es.

Su solución original está casi bien:

    p[0] = ast.UResta(-p[2])

El problema es que deben **representar** las operaciones, como siempre, mas no **efectuar** las operaciones — eso lo harán, pero luego.  No tiene sentido que hagan `-p[2]` porque eso sería efectuar una operación, y en `p[2]` no hay un número sino una representación abstracta de una manera de calcular un número.

La solución, claro, es representar la operación de tomar el negativo pero sin intentar efectuarla (por ahora):

    p[0] = ast.UResta(p[2])
