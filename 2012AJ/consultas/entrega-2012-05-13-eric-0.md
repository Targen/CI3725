1.  La expresión regular `\w+` que usan para los identificadores sería ideal en muchos lenguajes, pero acepta palabras con underscores, y ese caracter es inválido en AsGArD.

2.  El archivo principal a ejecutar debería llamarse `LexAsgard`, pero el suyo se llama `LexAsgard.py`.  Además, como no tiene una línea *shebang*/*hashbang*, es imposible ejecutarlo con el comando `./LexAsgard`, sino que habría que hacer algo como `python LexAsgard` aun si tuviera el nombre correcto.

3.  Su proyecto no imprime *tokens* ni errores si toda la entrada está en una sola línea por un problema en el flujo de ejecución en el ciclo que ejecuta el *lexer* al final de su archivo.  Todo lo genera como texto en la variable `aux` en esos casos.

4.  Cuando imprimen el *token* corespondiente a un identificador, deberían imprimir comillas dobles dentro de los paréntesis y al rededor del identificador.

5.  Su expresión regular para los comentarios es incorrecta.  No admite, por ejemplo, estos comentarios válidos:

        {-}-}
        {- } -}
        {- El caracter "}" es valido en un comentario
        si no le antecede un "-" que no sea el del
        inicio del comentario. -}`

6.  Su expresión regular para `TkOfType` es incorrecta.  Por ejemplo, este texto es transformado en una secuencia de `TkOfType` sin errores:

        of{type {- Pero { es un caracter invalido
                   a menos que inicie un comentario -}
        of}type {- Pero { es un caracter invalido
                   a menos que termine un comentario -}
        of.type {- Pero . por si solo es un caracter invalido -}
        of|type {- Pero | por si solo es un caracter invalido -}
        of-type {- Pero deberia dar dos TkIdent con
                   un TkResta en medio -}
        of,type {- Pero deberia dar dos TkIdent con
                   un TkComa en medio -}
        of;type {- Pero deberia dar dos TkIdent con
                   un TkPuntoYComa en medio -}
        of:type {- Pero deberia dar dos TkIdent con
                   un TkHorConcat en medio -}
        ofxtype {- Pero deberia ser TkIdent("ofxtype") -}
        ofcualquierpalabratype {- Igual que el anterior -}
        ofalgo_algotype {- Pero _ es un caracter invalido -}
        of type+ {- Deberia encontrar el TkOfType y luego
                    un TkSuma, pero solo encuentra el primero -}
        of type- {- Deberia encontrar el TkOfType y luego
                    un TkResta, pero solo encuentra el primero -}
        of type@ {- Deberia encontrar el TkOfType y luego
                    un error de caracter invalido por el @, pero
                    no detecta el error -}
