> 1- Sabemos que los comentarios son de la siguiente forma: "{- comentario -}", pero que sucede si
> No se cierra "-}", ? Es decir: "{- comentario ... Fin de archivo". Es tomado "{-" como caracter invalido o desde "{- hasta el fin d archivo" seria un comentario?

La sección 5 de la especificación del lenguaje dice

> En AsGArD es posible comentar secciones completas del programa, para que sean ignorados [sic] por el interpretador del lenguaje, **encerrando** dicho código entre los símbolos “`{-`” y “`-}`”.

(Énfasis mío.)  Según esta definición, un inicio de comentario que no tenga en alguna parte del archivo un fin de comentario que le corresponda no definiría un comentario, porque no hay texto *encerrado* entre esas secuencias.  En ese caso, el `{` sería un caracter inválido, el `-` sería el *token* correspondiente a la resta o el inverso aritmético, y todo lo demás se procesaría como código del programa.  Claro, como ya habrían encontrado un caracter ilegal, así que no les interesaría producir esos *tokens* sino encontrar más errores e imprimirlos todos al final.



- - -



> 2- En cuanto a la palabra reservada of type. Of junto a type forman una palabra reservada? O por separado tambien lo son? Es decir, of (sola) es palabra reservada y type (sola) tambien es palabra reservada? Si esto no fuera asi, entonces existe la posibilidad de que of sea un identificador, al igual que type.

El enunciado de la primera etapa del proyecto especifica que las palabras `of` y `type` juntas constituyen un único *token* en conjunto.  El problema es que no se especifica con mucha claridad lo que significa que estén juntas.  Consulté este asunto con los autores de la especificación (Ricardo y Carlos, los profesores de la teoría) y la aclaratoria fue que deben estar separadas por espacios en blanco de cualquier tipo y en cualquier cantidad.  Los espacios, tabuladores y fines de línea regulares son espacios en blanco, y también lo son los comentarios.  Por ejemplo, este código debería producir 7 *tokens* del tipo `TkOfType`:

    of type     {- Con un espacio normal        -}
    of	type    {- Con un tabulador             -}
    of
    type        {- Con un fin de línea          -}
    of         
           
       		          
             		   	
    type        {- Con muchos de los anteriores -}
    of{- Con un comentario -}type
    of{-Con-}{- varios -}{- comentarios -}type
    of    {- Con

          	        un-}	   	   {-
         poco de
    todo-}type

Este código, en cambio, solo debería producir *tokens* del tipo `TkIdent`:

    of type1
    oftype
    of typeof type
    Of type
    of typealgo
    algoof type
    type
    of



- - -



> Estoy utilizando python, y estoy teniendo problemas con el token of type, porque dado que uno de los caracteres ignorados es el espacio en blanco, primero reconoce a of como identificador, y luego type como identificador. He hecho algunos "trucos" con la finalidad de que reconozca el token completo, pero estos "trucos" me generan otros problemas cuando aparece of type o alguna variante de ella, algunos de ellos (son de soluciones/trucos distintos):
>
> - Si codigo de entrada tiene oftype lo reconoce como token de of type.
> - si aparece of type, muestra el token del of type, pero ademas reconoce a of como identificador..
> - y algunos otros...

Una de las cualidades peculiares y poco intuitivas de PLY es que el procesamiento de las expresiones regulares que se le especifiquen se realiza con una opción de la implantación de expresiones regulares de Python que hace que se ignoren los espacios en la expresión.  El propósito de ese modo de operación es que se puedan escribir expresiones regulares largas y complejas separando sus partes con espacios, incluso en múltiples líneas y con comentarios, sin que esos espacios y comentarios alteren sus significados.  A esto se debe que el *string* de Python `'of type'` reconozca las dos palabras juntas y no reconozca a dos palabras separadas por un espacio: PLY no ve ese espacio.  Una solución sencilla es escribir algo como `'of[ ]type'`, pero como también deben poder ser reconocidos *tokens* `TkOfType` con muchos espacios en blanco en medio, incluyendo comentarios, la expresión regular sería bastante más compleja que eso.  Pueden leer detalles sobre ese modo de operación en <http://docs.python.org/library/re.html#re.X>.

También deben tomar en cuenta la forma en la que PLY decide cuál *token* emitir cuando hay más de una posibilidad porque las expresiones regulares de dos o más *tokens* describan lenguajes que no son disjuntos.  En particular, `of type` puede reconocerse como un *token* `TkOfType`, pero también podría reconocerse un *token* `TkOf`.  La documentación de PLY (en <https://www.dabeaz.com/ply/ply.html>) especifica los mecanismos que tiene (su versión actual) para decidir entre las posibilidades situaciones de ambigüedad como esa.  En general, se emite el *token* correspondiente a la primera expresión regular cuyo reconocimiento sobre el inicio de la entrada resulte exitoso, y lo importante es determinar en qué orden se evalúan.
