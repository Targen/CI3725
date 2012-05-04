% Práctica 1: Expresiones regulares
% CI3725 (Traductores e interpretadores)
% Abril–Julio 2012

1.  Escriba expresiones regulares que denoten los siguientes lenguajes usando notación matemática:

    1.  El lenguaje de todas las palabras sobre el alfabeto $\{a, b\}$ de longitud al menos 2 en las que todas las $a$ preceden a las $b$.

    2.  El lenguaje de todas las palabras sobre el alfabeto $\{a, b\}$ en el que cada $a$ es inmediatamente seguida o precedida por una $b$.

    3.  El lenguaje de todas las palabras sobre el alfabeto $\{a, b\}$ en las que la $a$ ocurre un número par de veces y la $b$ ocurre un numero impar de veces.

    4.  El lenguaje de todas las palabras sobre el alfabeto $\{a, b, c\}$ que *no* incluyen a la subcadena $aba$.

2.  Demuestre que $(\omega^{R})^{R} = \omega$ por inducción sobre la longitud de la palabra $\omega \in \Sigma^{*}$.

3.  Escriba una expresión regular POSIX que denote el lenguaje de los identificadores válidos en el lenguaje de programación C++ junto con sus palabras reservadas siguiendo las siguientes reglas:

    1.  Todo identificador o palabra reservada comienza con un caracter alfabético o un `_`.

    2.  Ninguna palabra reservada contiene caracteres que no sean alfabéticos en minúsculas.

    2.  Ningún identificador puede tener dos `_` consecutivos.

    3.  Si un identificador comienza con un `_` seguido de otro caracter, éste no puede ser alfabético en mayúscula.
