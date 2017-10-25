# 2.2 - Programación Funcional

El paradigma de la programación funcional se centra en los valores más que en los efectos secundarios de la evaluación. En contraste con la programación imperativa, el estilo funcional se ocupa de los valores de las expresiones en un sentido matemático. La aplicación de funciones y la composición son conceptos de primera clase, y el estado mutable \(aunque obviamente existe en un nivel bajo\) se abstrae del programador.

Este es un concepto un tanto confuso, y a menudo es desconocido incluso para programadores experimentados. Los mejores paralelismos se extraen de las matemáticas, de las cuales se deriva la programación funcional.

Considere la ecuación matemática x = 3. El signo igual en esa expresión indica equivalencia: "x es igual a 3." Por el contrario, la afirmación de Ruby x = 3 es de naturaleza completamente diferente. Ese signo igual denota asignación: "asignar 3 a x". En un lenguaje de programación funcional, los iguales generalmente denotan igualdad en lugar de asignación. La diferencia clave aquí es que los lenguajes de programación funcionales especifican qué se va a calcular; los lenguajes de programación imperativos tienden a especificar cómo calcularlo.

### Funciones de orden superior

La piedra angular de la programación funcional, por supuesto, son las funciones. La principal forma en que el paradigma funcional influye en la programación de Ruby es en el uso de funciones de orden superior \(también llamadas funciones de primera clase, aunque estos dos términos no son estrictamente equivalentes\). Las funciones de orden superior son funciones que operan en otras funciones. Las funciones de orden superior generalmente toman una o más funciones como argumento o devuelven una función.

Ruby admite funciones principalmente como objetos de primera clase; pueden ser creados, manipulados, pasados, devueltos y llamados. Las funciones anónimas se representan como objetos Proc, creados con `Proc.new` o `Kernel#lambda`:

El uso más común para bloques en Ruby es en conjunto con iteradores. Muchos programadores que vienen a Ruby desde otros lenguajes de estilo más imperativo comienzan a escribir código como este:

```ruby
collection = (1..10).to_a    
for x in collection
    puts x 
end
```

La forma más similar a Ruby para expresar esto es usar un iterador, Array \# each, y pasarlo en un bloque. Esta es una segunda naturaleza para los programadores de Ruby experimentados:

`collection.each {|x| puts x}`

  
Este método es equivalente a crear un objeto Proc y pasarlo a cada uno

```
print_me = lambda{|x| puts x}    
collection.each(&print_me)
```

Todo esto es para mostrar que las funciones son objetos de primera clase y se pueden tratar como cualquier otro objeto.

### Enumerable

El módulo Enumerable de Ruby proporciona varios métodos de conveniencia para mezclar en clases que son "enumerables" o pueden repetirse. Estos métodos se basan en un método de cada instancia y, opcionalmente, el método &lt;=&gt; \(comparación o "nave espacial"\). Los métodos de Enumerable se dividen en varias categorías.

### Enumerador

Ruby tiene otro truco poco conocido en la manga, y ese es el Enumerator de la biblioteca estándar. \(Como está en la biblioteca estándar y no en el lenguaje central, debe requerir "enumerador" para usarlo\).

Enumerable proporciona muchos iteradores que se pueden usar en cualquier objeto enumerable, pero tiene una limitación: todos los iteradores se basan en el método de cada instancia. Si desea utilizar un iterador distinto de cada uno como base para el mapa, la inyección o cualquiera de las otras funciones en Enumerable, puede usar Enumerator como un puente.

### Código de enrutamiento de rails

El código de enrutamiento de Rails es quizás uno de los códigos conceptualmente más difíciles en Rails. El código enfrenta varias restricciones:

• Los segmentos de ruta pueden capturar varias partes de la URL:

- Los controladores pueden tener un espacio de nombres, por lo que la ruta ": controller /: action /: id" puede coincidir con la URL "/ store / product / edit / 15", con el controlador como "store / product".

- Las rutas pueden contener segmentos path\_info que desestructuran múltiples segmentos de URL: la ruta "page / \* path\_info" puede coincidir con la URL "/ page / products / top\_products / 15", y el segmento path\_info captura el resto de la URL.

• Las rutas pueden estar restringidas por condiciones que deben cumplirse para que la ruta coincida.

• El sistema de enrutamiento debe ser bidireccional; se avanza para reconocer las rutas y en reversa para generarlas.

• El reconocimiento de rutas debe ser rápido porque es necesario ejecutar la solicitud de HTTP. La generación de rutas debe ser muy rápida porque puede ejecutarse decenas de veces por solicitud HTTP \(una vez por enlace de salida\) al generar una página.



