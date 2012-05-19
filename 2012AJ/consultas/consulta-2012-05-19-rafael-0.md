> En teoría nos dijeron que un autómata finito determinístico NO podía tener lambda transiciones; más al hacer el autómata finito no determinístico correspondiente a la unión de las 3 E.R (pregunta 3), ajuro quedan lambda transiciones para pasar de un estado inicial nuevo a los estados iniciales de cada una de las máquinas, más la lambda transición que tiene la máquina 3 para hacer la clausura de kleene.  Al aplicar el algoritmo de hacer ese automáta no determinístico - determinístico, se logra el objetivo de hacerlo determinista, más sigue teniendo lambda transiciones. ¿Está esto mal? ¿La nueva máquina determinista puede seguir teniendo lambda transiciones?

Eso estaría mal.  Una transición $\lambda$ en un $\lambda$-NFA

\begin{equation}
    M = (Q, F, q_{0}, \delta)
\end{equation}

se manifiesta cuando existe un estado

\begin{equation}
    q_{i} \in Q
\end{equation}

tal que

\begin{equation}
    \delta(q_{i}, \lambda) \neq \emptyset
\end{equation}

Esto tiene sentido en un $\lambda$-NFA porque

\begin{equation}
    \delta : Q \times (\Sigma^{1}\cup\{\lambda\}) \rightarrow \mathcal{P}(Q)
\end{equation}

La definición de los $\lambda$-NFAs admite la posibilidad de efectuar una transición de la máquina sin consumir símbolos de la entrada porque su función de transición tiene esta forma: recibe un estado y una palabra cualquiera de longitud cero o uno sobre el alfabeto, y produce un conjunto de posibles estados destino.  Si la palabra que recibe la función de transición es de tamaño 1, entonces esa transición consume el símbolo de la palabra; si es de tamaño cero, no consume ningún símbolo.  Las transiciones $\lambda$ corresponden a los resultados no nulos de δ con algún estado y con la palabra vacía.

En cambio, la definición de los DFAs utiliza otra forma para la función de transición:

\begin{equation}
    \delta : Q \times \Sigma \rightarrow Q
\end{equation}

En los DFAs, la función de transición recibe un estado y exactamente un símbolo que es tomado de la entrada.  Como tiene que ser exactamente un símbolo, no puede ser una palabra vacía, y no es posible tener transiciones que no consuman exactamente un símbolo, como las transiciones $\lambda$ que consumen cero símbolos.

Más intuitivamente, un autómata con transiciones $\lambda$ no puede ser considerado directamente determinístico por una razón que va más allá de las restricciones de la definición formal.  Un autómata determinístico es uno en el cual el estado alcanzado al ejecutar la máquina con cualquier palabra es uno y solo uno al terminar de consumirla.  Si un autómata tiene una transición $\lambda$ entre un estado qᵢ y otro estado qⱼ, entonces siempre que la máquina llegue al estado qᵢ al terminar de consumir su entrada, podría también haber llegado al estado qⱼ.  Como habría más de una posibilidad, no habría determinismo.

Los algoritmos de transformación de $\lambda$-NFA a DFA nunca producen transiciones $\lambda$; si lo hicieran, ni siquiera serían consistentes con las definiciones formales de lo que es un $\lambda$-NFA y lo que es un DFA, y tampoco serían consistentes con lo que se entiende por determinismo.
