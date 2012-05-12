> Buenos dias, tengo la siguiente duda, cuando imprimimos los tokens, esta impresion debe ser lineal, o debe imprimirse un token en cada linea? O da igual la forma en que decidamos imprimir??
>
> Ejemplo:
>
>     TkUsing TkOfType TkIdent("hola") TkLienzo("</>")
>
> o
>
>     TkUsing
>     TkOfType
>     TkIdent("hola")
>     TkLienzo("</>")

El enunciado de la primera etapa en efecto especifica ese detalle del formato de salida con poca precisión: se presentan dos ejemplos donde se muestra la representación textual de varios *tokens* por línea separados por espacios regulares, pero hay varias líneas.

Presumo que la razón de esa imprecisión es que no es muy importante: separar las representaciones textuales de los *tokens* con fines de línea o con espacios regulares es equivalente para lo que importa: que la salida pueda reconocerse sin ambigüedad.

Un detalle que sí está especificado y que deben tomar en cuenta es que la representación textual de los *tokens* de literales de lienzo no debe incluir a los símbolos `<` ni `>` según el punto 5 de la primera lista del enunciado de la primera etapa del proyecto:

>   *   Los literales lienzos, los cuales serán uno de los siguientes: `<empty>`, `</>`, `<\>`, `<|>`, `<_>`, `<->` o `< >`.  Todos estos serán representados por el token `TkLienzo`, parametrizado por el contenido envuelto entre los sìmbolos `<` y `>`.  Por ejemplo, el literal de lienzo `</>` será representado por `TkLienzo("/")`.
