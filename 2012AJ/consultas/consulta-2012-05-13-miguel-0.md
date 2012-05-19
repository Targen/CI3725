> Como debe reconocer la siguiente frase el lexer: "of type;" como TkOfType TkPuntoYComa o de otra manera ?

Exactamente de esa manera.

> El problema para reconocerlo asi es que solo se me ocurre de manera recursiva que cuando lea el of lea la siguiente y decida... Pero no se si esto sea correcto... LO es?
>
> Hasta ahora yo tengo la siguiente expresion regular :
>
>     TkOfType = r'of' + r'((\s)|{-(.|\n)*?-})+type(\s|{-(.|\n)*?-})'
>
> Pero con esa no puedo hacer lo ya mencionado, y creo que no podria hacerse mediante una expresion regular...

Esa expresión regular casi hace lo que se debe hacer (aunque creo que tendrían que escapar los `{` y `}`.  Al menos hasta `type` está bien.  El problema es lo que está al final.

El problema es que están obligando a que luego del final del *token* haya un espacio, pero en efecto podría haber algún símbolo que corresponda a otro *token*.  El problema sería, entonces, reconocer que después de `type` haya algo, pero sin consumirlo.  Si están usando Ruby o Python, eso se puede lograr con un *lookahead*: es una forma de decirle a la expresión regular que algo debe ser reconocido pero que no se consuma la entrada de lo que se reconoció.

Si no pueden usar *lookaheads*, también es posible hacerlo usando algo como `\b` o `\>`, que son cosas similares a *lookaheads* (también son aserciones de ancho cero — no consumen caracteres), pero predefinidas: `\b` reconoce una *frontera de palabra*, que es cualquier posición entre dos caracteres tal que uno de los dos pes reconocido por `[a-zA-Z0-9_]` y el otro no; `\>` reconoce un *final de palabra*, que es lo mismo que una frontera de palabra pero solo el lado del final, no el comienzo (y hay algunos motores de expresiones regulares que no implantan la aserción `\>`).

En el caso de Alex (con Haskell) creo que no hay ninguna aserción de ancho cero implantada, así que el problema debe resolverse consumiendo el caracter que sigue a `type` y devolviéndolo a la entrada luego al darse cuenta de que lo que se encontró fue un `TkOfType`.  Claro, esa misma técnica también funciona en los demás lenguajes, pero es engorrosa.  Y tiene un problema adicional: si el *token* `TkOfType` es lo último que tiene el programa, entonces `type` no es seguido por ningún caracter, sino por el fin de la entrada.  Manejar esos casos bordes es incómodo y es mejor escribir una expresión regular que haga el trabajo si su herramienta lo soporta.
