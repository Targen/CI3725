> Tengo ciertas dudas con la parte teorica-practica de la primera etapa del
> proyecto.
>
> Primero, en la 1 la expresion E3 debe reconocer a los identificadores, a que
> refieren con identificadores? (yeah...Im truly lost...)

La expresión regular E3 debería denotar el lenguaje de los identificadores (o nombres) de variables en AsGArD.  El tercer párrafo de la sección 0 del documento de especificación del lenguaje AsGArD dice

> Cada identificador estará formado por una letra seguida de cualquier cantidad de letras y dígitos decimales.

Para escribir esa expresión regular (y todas las demás de la revisión teórico-práctica) deberían limitarse a la notación matemática para expresiones regulares que se ha usado en las clases de teoría.  Claro, es bastante incómodo describir conjuntos de caracteres en esa notación como los que se usarían en código con cosas como `[a-zA-Z]`, así que podrían usar una notación abreviada como `a + b + ... + z + A + B + ... + Z`, o algo por el estilo.



- - -



> Ahora, en la segunda parte cuando refieren a la unión del lenguaje, es unir
> la maquina M1, con M2 y M3?, o sea tipo un estado inicial que tenga 3 lamdas
> que conecten las maquinas y lleguen al mismo estado final? De ser así, que
> me preguntan realmente en la 4? Y a que juegan con nosotros en la 5?
> (really...) 

En efecto, cuando se usa la técnica vista en clase para obtener un autómata para la unión de varios lenguajes dados autómatas para cada uno, resulta deseable que el autómata para la unión tenga un solo estado final.  Para que esto pase, se agrega un nuevo estado (que se hace final) y se hace que todos los estados finales de las máquinas a unir vayan al nuevo estado final con transiciones λ.

Sin embargo, condensar los estados finales de la máquina para la unión en un solo estado hace que no se pueda distinguir cuál camino fue tomado para llegar a reconocer la palabra; aunque sí podría conocerse esa información viendo las transiciones completas de la máquina, no sería posible saberlo si lo único que se conoce es a cuál de los estados finales llegó la última transición.

Un *lexer* necesita esa información para decidir qué *token* emitir cuando se reconoce una parte de la entrada.  Por lo tanto, cuando construyan el autómata de la unión en este ejercicio, es importante que **no** hagan que solo tenga un único estado final.

En otros ejercicios es útil hacer que haya un único estado final, porque así se pueden construir máquinas que solo tienen un punto de entrada y un punto de “salida” (o de reconocimiento exitoso), y eso facilita la prueba constructiva de la existencia de un autómata finito que reconoce el mismo lenguaje que cualquier expresión regular.  Pero en el caso de estos ejercicios, eso no es deseable.



Concretamente, la pregunta 4 pide hacer corresponder cada estado final del autómata de la unión con algún tipo de *token* (que es lo mismo que hacer la correspondencia con alguno de los lenguajes de E1, E2 y E3).

La pregunta 5 se refiere a la existencia de ciertas palabras que pueden ir a parar a más de uno de esos estados finales cuando se corren sobre el autómata de la unión, así que no se sabría cuál de los *tokens* emitir.  La pregunta 8 tiene que ver con tomar la decisión de cuál de los *tokens* posibles emitir en esos casos conflictivos o ambiguos.
