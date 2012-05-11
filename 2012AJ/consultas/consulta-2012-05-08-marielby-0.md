> En el enunciado dice que la entrada será por entrada estándar. Ahora, se introduce el código a analizar y para indicar que terminó, es valido que deba pulsar control D para indicar el eof, o se debe utilizar un archivo y redireccionar?

Es equivalente en todos los casos que importan.

La manera más sencilla de hacer el flujo principal de ejecución de un *lexer* (y todo lo que le sigue) es leer la totalidad de la entrada a una cadena de caracteres en memoria y trabajar únicamente con eso.  No es lo ideal: sería preferible leer en forma incremental y perezosa, consumiendo caracteres de la entrada estándar y ejecutando poco a poco las transiciones del autómata que produce los *tokens*.  Haskell con Alex hace eso de forma prácticamente transparente; en los demás lenguajes probablemente se podría programar, pero no es demasiado práctico, así que es mejor usar la solución costosa pero sencilla.

La diferencia en el comportamiento de leer poco a poco o leer todo antes de analizar sería que si escriben la entrada directamente en el terminal, podría empezar a reconocerse antes de que terminen si leen poco a poco, mientras que en el caso normal solo se emitirían *tokens* cuando la entrada estándar indique que no hay más datos por leer.  Al final es equivalente, y eso no cambia el resultado en absoluto.  No es algo que haga falta tomar en cuenta.



- - -



> [estoy trabajando con python]  Se debe contar con un main? En caso de que sea así, es en el donde se lee la entrada, se obtienen los tokens y se imprime? O es indiferente?

A diferencia de lo que suele hacerse en la mayoría de los lenguajes compilados, los lenguajes imperativos interpretados y dinámicos como Python, Ruby, Perl y muchos más no definen su punto de entrada en un procedimiento con un nombre especial, como el típico `main`, sino que el código se ejecuta desde el inicio del archivo a interpretar.  A pesar de que es totalmente innecesario para lograr que el código se ejecute, es común encontrar código en Python estructurado así:

~~~{.Python}
def main():
        # Código principal del programa
        ...

if __name__ == '__main__':
        main()
~~~

Las razones para hacer esto incluyen que pudiera querer usarse ese procedimiento principal en forma recursiva (al darle un nombre se vuelve posible llamarlo explícitamente), y que podría ser deseable evitar que se ejecute el programa si el código no es ejecutado directamente sino que es cargado por otro módulo para reutilizar parte de su funcionalidad.  Si un archivo estructurado así se carga directamente por la ejecución de ese archivo de código sobre el interpretador, el cuerpo del condicional se ejecuta y se corre esa función; si la carga de ese código fue indirecta, el cuerpo del condicional no se ejecuta, así que la función `main` no corre, pero sí se cargan todas las definiciones del archivo (y se ejecutan las demás instrucciones que estén al nivel raíz).

En resumen: no es necesario, pero pueden hacerlo si por alguna razón les conviene por el diseño de su código.



- - -



> En el enunciado se indica que debemos entregar un breve informe. Esto que debe contener?

El informe que deben entregar está compuesto de dos partes:

1.  Una explicación breve de las decisiones de diseño que tomaran en su implantación del analizador lexicográfico.  La idea no es que digan cosas obvias, como que el proyecto es un *lexer* o que usaron expresiones regulares, sino cualquier detalle que les parezca relevante sobre su implementación, como las técnicas que usaron para escribir las expresiones regulares más complicadas, o una explicación básica del flujo de ejecución del *lexer* si lo hacen a mano (para los que usan Ruby), o cualquier cosa de ese estilo.

    Es importante destacar la manera en la que se especifica que deben entregar esta parte del proyecto: se habla de un “*_breve_* informe”, y la palabra “breve” está subrayada y resaltada en itálicas.  No se compliquen demasiado con eso: la idea es que todo proyecto de software, por simple que sea, implica ciertas decisiones básicas de diseño, de diseño de la implantación, y de implantación (que suena redundante, pero no lo es), y es bueno documentar esas decisiones.

2.  Las respuestas a los problemas de la *revisión teórico-práctica*.  Esta parte del informe originalmente se entregaría junto con la primera parte y con el código, pero su entrega fue postergada por una semana porque algunos de los problemas requieren que conozcan partes de la teoría del curso que aun no se han cubierto en clase por cambios en el cronograma.

Por lo que más quieran, no lo hagan con Office ni nada parecido.  Texto plano, Markdown o afines que son casi lo mismo, LaTeX si tienen mucho tiempo libre, o árbol muerto si prefieren lo *retro*.  Al que me envíe un `.doc` le instalo Windows Millenium Edition.
