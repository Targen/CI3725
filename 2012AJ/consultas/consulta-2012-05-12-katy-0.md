Revisé su código por encima y encontré varios problemas:

1.  La lectura de la entrada la hacen únicamente de un archivo fijo llamado `prueba.txt`, pero deberían obtener el programa a analizar únicamente de la entrada estándar.

2.  La línea *shebang* o *hashbang* que tienen está mal:

        #!usr/bin/python

    debería comenzar con `#!` y luego tener el camino absoluto a un ejecutable que esté en una posición conocida del sistema.  En particular, como no comienza con un `/` (después del `#!`), no es un camino absoluto.

3.  El archivo a ejecutar debería llamarse precisamente `LexAsgard`, pero el suyo se llama `LexAsgard.py`.

4.  El formato de salida no es el especificado.  En el caso de los *tokens* que no almacenan datos, no deberían imprimir nada aparte del nombre del tipo de *token*; por ejemplo, si encuentran una palabra reservada `using`, deberían imprimir únicamente `TkUsing` y más nada (para ese *token*).

    En el caso de *tokens* que almacenen algún dato, como los de identificadores y de literales numéricos y de lienzos, deben imprimir entre paréntesis el dato almacenado en el formato especificado por el enunciado de la primera etapa inmediatamente después del nombre del tipo de *token*; por ejemplo, si encuentran el literal numérico `42`, deberían imprimir únicamente `TkNum(42)`, y si encuentran el literal de lienzo `<->`, deberían imprimir únicamente `TkLienzo("-")`.

5.  Consideren este texto:

        using {- Este programa debería dar errores
        lexicográficos, pero -} es reconocido
        correctamente! -} using

    Los comentarios en AsGArD no pueden contener a la secuencia `-}`, por lo que ese texto no es un programa válido en AsGArD.  En particular, un *lexer* para AsGArD podría reconocer el primer `using` como un `TkUsing`, luego ignorar el espacio, luego ignorar todo hasta el primer fin de comentario (luego de la palabra `pero`), luego ignorar el espacio antes de la palabra `es`, luego reconocer tres identificadores (e ignorar dos espacios en medio, claro), y luego darse cuenta de que hay un error: el signo de exclamación es un caracter inválido, y también lo será el segundo caracter `}`.

    Su expresión regular para los comentarios está escrita de forma tal que se toma como comentario desde el primer `{-` hasta el último `-}` de un programa.  Sin embargo, los comentarios deberían cerrarse con el fin de comentario más cercano, no con el más lejano.
