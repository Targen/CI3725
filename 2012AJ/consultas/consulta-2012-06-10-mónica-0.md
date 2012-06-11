> Mira aun no tengo muy claro como hacer lo del árbol. Es decir, a medida que
> el parser va recorriendo la gramática se va armando el árbol, y luego lo
> mando a imprimir, pero no entiendo muy bien la estructura de ese árbol.
> En el anunciado hablan de una clase para las expresiones binarias, lo que me
> parece medio útil, pero también sugieren hacer un árbol para las
> instrucciones y otro para las expresiones, eso no lo veo muy claro. 
>
> Gracias por la ayuda XD

No piensen en árboles como las estructuras matemáticas estudiadas en los cursos de algoritmos y estructuras.  Piensen, en cambio, en anidamiento de objetos.  Usaré seudocódigo similar a Ruby para un ejemplo por comodidad, pero el concepto se extiende al menos a todas las demás herramientas con orientación a objetos:

Supongan que quieren hacer ASTs para un pequeño lenguaje que hace aritmética super básica:

    Programa →
        'precisión' '=' 'número ':' Instrucciones

    Instrucciones →
        Instrucciones ';' Instrucción
      | Instrucción

    Instrucción →
        'mostrar' Expresión '.'
      | 'variable' '=' Expresión '.'

    Expresión →
        Expresión '+' Expresión
      | - Expresión
      | '(' Expresión ')'
      | 'número'

Como el objetivo es tener una representación abstracta de todo lo que diga un programa, hay que definir clases para cada estructura sintáctica del lenguaje que signifique algo:

    class Programa
      member precisión
      member instrucciones

      def constructor(param_precisión, param_instrucciones)
        self.precisión = param_precisión
        self.instrucciones = param_instrucciones
      end

      def show
        return \
          "Programa" \
          + "- precisión: " + self.precisión.to_string \
          + "- instrucciones: " + self.instrucciones.each(show)
      end
    end


    class Mostrar
      member expresión

      def constructor(param_expresión)
        self.expresión = param_expresión
      end

      def show
        return \
          "Mostrar" \
          + "- expresión: " + self.expresión.to_string
      end
    end


    class Asignar
      member variable
      member expresión

      def constructor(param_variable, param_expresión)
        self.variable = param_variable
        self.expresión = param_expresión
      end

      def show
        return \
          "Asignar" \
          + "- variable: " + self.variable + "\n" \
          + "- expresión: " + self.expresión.to_string
      end
    end


    class Suma
      member izquierda
      member derecha

      def constructor(param_izquierda, param_derecha)
        self.izquierda = param_izquierda
        self.derecha = param_derecha
      end

      def show
        return \
          "Suma" \
          + "- izquierda: " + self.izquierda.show() \
          + "- derecha: " + self.derecha.show()
      end
    end


    class Negativo
      member expresión

      def constructor(param_expresión)
        self.expresión = param_expresión
      end

      def show
        return \
          "Negativo" \
          + "- expresión: " + self.expresión.show()
      end
    end


    class Número
      member valor

      def constructor(param_valor)
        self.valor = param_valor
      end

      def show
        return \
          "Número" \
          + "- valor: " + self.valor.to_string
      end
    end



Luego de tener esto definido, es posible aumentar la gramática con acciones semánticas que usen a esas clases para representar la estructura del árbol:

    P →
        'precisión' '=' 'número' ':' Is { new Programa($1, $5) }

    Is →
        Is ';' I { $1 + [$3] }
      | I { [$1] }

    I →
        'mostrar' E '.' { new Mostrar($2) }
      | 'variable' '=' E '.' { new Asignación($1, $3) }

    E →
        E '+' E { new Suma($1, $3) }
      | - E { new Negativo($2) }
      | '(' E ')' { $2 }
      | 'número' { new Número($1) }



Al final, el método `parse` del analizador sintáctico generado debe retornar una instancia de la clase `Programa` a quien le podrán llamar el método `show` para obtener el *string* de su representación textual, que es lo que deben imprimir si no hay errores.

Esa es la idea básica.
