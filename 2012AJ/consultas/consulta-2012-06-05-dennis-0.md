> Quería preguntarte si podrías explicarme un poco mejor el uso del "do_parse" y el "next_token ", según tengo entendido se usan para poder obtener los tokens y su información, pero no me queda claro cómo usarlos correctamente.

La sección `inner` de un archivo de fuente para Racc permite escribir código que irá dentro de la definición de la clase `Parser` que se genera.  En esa sección debe definirse su método `next_token`, que será llamado por el propio *parser* en el proceso de reconocimiento para obtener cada uno de los *tokens*, uno a uno.

El método `next_token` debe retornar una lista de Ruby con exactamente dos elementos.

El primer elemento debe ser un objeto que represente al tipo de *token* que se encontró; si todos sus *tokens* se representan como instancias de clases diferentes, una para cada tipo de *token*, entonces la propia clase del token encontrado es un buen candidato para lo que querrán retornar en el primer elemento de `next_token`.  También serviría el nombre de la clase o el símbolo del nombre de la clase.

El punto es que ahí no deben poner el *token* específico, sino algo que sea igual para todos los *tokens* de ese tipo.  Si es un identificador “foo”, no deberían poner algo como `TkIdent(foo)`, sino algo como `TkIdent`, `"TkIdent"`, `:TkIdent`, o algo por el estilo.

El segundo elemento de la lista de dos elementos que deben retornar, en cambio, es el valor específico asociado al *token* particular que fue encontrado.  Ahí tiene más sentido retornar el valor completo del *token* que encontraron.

Al acabar la lista de *tokens*, el método `next_token` debe indicar esta condición retornando `false` en el primer elemento de la lista.  En ese caso, el segundo elemento se ignora, así que no importa qué pongan ahí.

Si su analizador lexicográfico es un objeto almacenado en el miembro llamado `lexer` del objeto de la clase `Parser`, y el método de `lexer` que retorna el próximo *token* se llama `yylex`, una definición podría ser similar a esto:

    def next_token
      token = @lexer.yylex
      return [false, false] unless token
      return [token.class, token]
    end



El objeto que hace el análisis sintáctico (que es una instancia de la clase `Parser` generada por Racc) tendrá, además, un método llamado `do_parse` que inicia el reconocimiento.  La idea es que la instancia de la clase `Parser` debe haber sido creada antes para poder suministrarle los datos (a través de un objeto `lexer`, en el ejemplo que les puse, que tiene un método que produce los *tokens* uno a uno), y luego solo es necesario llamar a `do_parse` (sin parámetro alguno) para hacer todo el trabajo.

`do_parse` internamente llama a `next_token` para obtener la secuencia de *tokens* a analizar.  Si el reconocimiento falla por algún error de sintaxis, El valor retornado por `do_parse` será el resultado asignado al atributo semántico de la última producción con la que se aplicara una producción; es decir, es el atributo asignado al símbolo inicial de la gramática.  Debería retornar, entonces, el objeto en la raíz de su árbol sintáctico abstracto.

Si se encuentran errores de sintaxis, `do_parse` llama al método `on_error`, y si no suministran una definición de ese método, lo que hace es lanzar una excepción que pueden atrapar para mostrar mensajes de error.

El método `do_parse` **no** debería ser definido por ustedes, pero sí deberían usarlo en su código —quizás en el programa principal, o mejor, en otro método— para efectuar el proceso de reconocimiento.
