# 2.1 - Técnicas Fundamentales

Desde su lanzamiento inicial en julio de 2004, el framework web Ruby on Rails ha ganado popularidad constantemente. Rails ha estado convirtiendo a los desarrolladores de PHP, Java y .NET de una manera más simple: una arquitectura modelo-vista-controlador \(MVC\), valores predeterminados razonables \("convención sobre configuración"\) y el poderoso lenguaje de programación Ruby.

Rails tenía una mala reputación por la falta de documentación durante su primer año o dos. Esta brecha ha sido completada por los miles de desarrolladores que usan, contribuyen y escriben sobre Ruby on Rails, así como también por el proyecto de documentación de Rails \(http://railsdocumentation.org/\). Hay cientos de blogs que ofrecen tutoriales y consejos para el desarrollo de Rails.

El objetivo de este libro es recopilar y extraer las mejores prácticas y el conocimiento incorporado por la comunidad de desarrolladores de Rails y presentar todo en un formato compacto y fácil de entender para programadores experimentados. Además, busco presentar facetas del desarrollo web que a menudo son subestimadas o descartadas por la comunidad de Rails.

### ¿Qué es la metaprogramación?

Rails trajo la metaprogramación a las masas. Aunque ciertamente no fue la primera aplicación en utilizar las amplias instalaciones de Ruby para la introspección, es probablemente la más popular. Para entender Rails, primero debemos examinar las partes de Ruby que hacen posible Rails. Este capítulo sienta las bases para las técnicas discutidas en el resto de este libro.

La metaprogramación es una técnica de programación en la cual el código escribe otro código o hace una introspección sobre sí mismo. El prefijo meta- \(del griego\) se refiere a la abstracción; el código que usa técnicas de metaprogramación funciona en dos niveles de abstracción simultáneamente.

La metaprogramación se utiliza en muchos lenguajes, pero es más popular en los lenguajes dinámicos porque generalmente tienen más capacidades de tiempo de ejecución para manipular código como datos. Aunque la reflexión está disponible en más lenguajes estáticos como C \# y Java, no es tan transparente como en los lenguajes más dinámicos, como Ruby, porque el código y los datos están en dos niveles separados en tiempo de ejecución.

La introspección generalmente se realiza en uno de dos niveles. La introspección sintáctica es el nivel más bajo de examen directo de introspección del texto del programa o de la secuencia token. La metaprogramación basada en macros y basada en plantillas generalmente opera a nivel sintáctico.

Lisp alienta este estilo de metaprogramación mediante el uso de expresiones S \(esencialmente una traducción directa del árbol de sintaxis abstracta del programa\) tanto para código como para datos. La metaprogramación en Lisp implica macros, que son esencialmente plantillas para código. Esto ofrece la ventaja de trabajar en un nivel; el código y los datos se representan de la misma manera, y lo único que distingue el código de los datos es si se evalúa. Sin embargo, hay algunos inconvenientes en la metaprogramación a nivel sintáctico. La captura de variables y la evaluación múltiple inadvertida son consecuencias directas de tener un código en dos niveles de abstracción en la fuente evaluada en el mismo espacio de nombres. Aunque existen modismos Lisp estándar para tratar estos problemas, representan más cosas que el programador Lisp debe aprender y pensar.

La introspección sintáctica para Ruby está disponible a través de la biblioteca ParseTree, que traduce la fuente de Ruby a S-expressions. \* Una aplicación interesante de esta biblioteca es Heckle, un marco de prueba que analiza el código fuente de Ruby y lo muta, cambiando cadenas y volteando a falso y viceversa La idea es que si tiene una buena cobertura de prueba, cualquier mutación de su código podría causar que las pruebas de su unidad fallen.

La alternativa de alto nivel a la introspección sintáctica es la introspección semántica, o el examen de un programa a través de las estructuras de datos de alto nivel del lenguaje. Exactamente cómo se ve esto difiere entre los idiomas, pero en Ruby generalmente significa trabajar a nivel de clase y método: crear, reescribir y aliasar métodos; interceptando llamadas de método; y manipulando la cadena de herencia. Estas técnicas suelen ser más ortogonales al código existente que los métodos sintácticos, porque tienden a tratar los métodos existentes como cuadros negros en lugar de hurgar en sus implementaciones.

### No te repitas

En un nivel alto, la metaprogramación es útil para trabajar hacia el principio DRY \(No repetir\). También conocido como "Once and Only Once" \(Una y única vez\), el principio DRY dicta que solo debe expresar una determinada información una vez en un sistema. La duplicación generalmente no es necesaria, especialmente en lenguajes dinámicos como Ruby. Así como la abstracción funcional nos permite evitar la duplicación de código que es el mismo o casi el mismo, la metaprogramación nos permite evitar la duplicación de conceptos similares cuando se repiten en una aplicación.

La metaprogramación se trata principalmente de simplicidad. Una de las maneras más sencillas de familiarizarse con la metaprogramación es buscar código repetido y factorizarlo. El código redundante puede tenerse en cuenta en las funciones; las funciones o patrones redundantes a menudo se pueden eliminar mediante el uso de la metaprogramación.

> Los patrones de diseño cubren el territorio superpuesto aquí; los patrones están diseñados para minimizar la cantidad de veces que tiene que resolver el mismo problema. En la comunidad de Ruby, los patrones de diseño han adquirido algo de una reputación negativa. Para algunos desarrolladores, los patrones son un vocabulario común para describir soluciones a problemas recurrentes. Para otros, están sobreinyectados.
>
> Para estar seguro, los patrones se pueden aplicar en exceso. Sin embargo, este no es el caso si se usan de forma juiciosa. Los patrones de diseño solo son útiles en la medida en que reducen la complejidad cognitiva. En Ruby, algunos de los patrones de grano fino son tan transparentes que sería contradictorio llamarlos "patrones"; en realidad son modismos, y la mayoría de los programadores que "piensan en Ruby" los usan sin pensar. Los patrones deben considerarse como un vocabulario para describir la arquitectura, no como una biblioteca de soluciones de implementación preempaquetadas. Los buenos patrones de diseño de Ruby son muy diferentes de los buenos patrones de diseño de C ++ en este aspecto.

En general, la metaprogramación no debe usarse simplemente para repetir el código. Siempre debe evaluar las opciones para ver si otra técnica, como la abstracción funcional, se adapta mejor al problema. Sin embargo, en algunos casos, repetir código a través de metaprogramación es la mejor manera de resolver un problema. Por ejemplo, cuando se deben definir varios métodos muy similares en un objeto, como en los métodos de ayuda de ActiveRecord, se puede usar la metaprogramación.

### Advertencias

El código que se reescribe a sí mismo puede ser muy difícil de escribir y mantener. Los dispositivos de programación que elijas siempre deben servir a tus necesidades: te harán la vida más fácil, no más difícil. Las técnicas ilustradas aquí deberían ser más herramientas en su caja de herramientas, no las únicas herramientas.

### Programación ascendente

La programación ascendente es un concepto tomado del mundo Lisp. El concepto principal en la programación de abajo arriba es construir abstracciones desde el nivel más bajo. Al escribir las construcciones de nivel más bajo primero, esencialmente estás construyendo tu programa sobre esas abstracciones. En cierto sentido, estás escribiendo un lenguaje específico de dominio en el que construyes tus programas.

Este concepto es extremadamente útil en ActiveRecord. Después de crear sus objetos básicos de esquema y modelo, puede comenzar a crear abstracciones en la parte superior de esos objetos. Muchos proyectos de Rails comienzan creando abstracciones en el modelo como este, antes de escribir una sola línea de código de controlador o incluso diseñar la interfaz web:

```ruby
class Order < ActiveRecord::Base
   has_many :line_items
   def total
     subtotal + shipping + tax
   end
   def subtotal
     line_items.sum(:price)
   end
   def shipping
     shipping_base_price + line_items.sum(:shipping)
   end
   def tax
     subtotal * TAX_RATE
   end 
end
```

### Técnicas de Metaprogramación

Ahora que hemos cubierto los fundamentos de Ruby, podemos examinar algunas de las técnicas de metaprogramación comunes que se usan en Rails.

Aunque escribimos ejemplos en Ruby, la mayoría de estas técnicas son aplicables a cualquier lenguaje de programación dinámica. De hecho, muchos de los modismos de metaprogramación de Ruby son descaradamente robados de Lisp, Smalltalk o Perl.

#### Retrasando la búsqueda de métodos hasta el tiempo de ejecución

A menudo queremos crear una interfaz cuyos métodos varíen dependiendo de algunos datos de tiempo de ejecución. El ejemplo más destacado de esto en Rails es el método de acceso de atributos de ActiveRecord. Las llamadas a métodos en un objeto ActiveRecord \(como `person.name`\) se traducen en tiempo de ejecución para atribuir accesos. En el nivel de método de clase, ActiveRecord ofrece una flexibilidad extrema: `Person.find_all_by_user_id_and_active(42, true)` se traduce a la consulta SQL adecuada, lo que aumenta la excepción estándar NoMethodError si esos atributos no existen.

La magia detrás de esto es el método `method_missing` de Ruby. Cuando se invoca un método inexistente en un objeto, Ruby primero verifica la clase de ese objeto para un método `method_missing` antes de generar un NoMethodError. El primer argumento de `method_missing` es el nombre del método llamado; el resto de los argumentos corresponde a los argumentos pasados ​​al método. Cualquier bloque pasado al método se pasa a `method_missing`. Entonces, una firma de método completa es:

```ruby
def method_missing(method_id, *args, &block)
    ...
end
```

Hay varias desventajas al usar `method_missing`:

* Es más lento que la búsqueda de métodos convencionales. Las pruebas simples indican que el envío del método con `method_missing` es al menos dos o tres veces más caro que el envío convencional.
* Dado que los métodos que se están llamando nunca existen en realidad, solo se interceptan en el último paso del proceso de búsqueda de métodos, no se pueden documentar ni introspectar como lo hacen los métodos convencionales.
* Debido a que todos los métodos dinámicos deben pasar por el método `method_missing`, el cuerpo de ese método puede llegar a ser bastante grande si hay muchos aspectos diferentes del código que necesitan agregar métodos dinámicamente.
* El uso de `method_missing` restringe la compatibilidad con versiones futuras de una API. Una vez que confía en `method_missing` para hacer algo interesante con métodos indefinidos, la introducción de nuevos métodos en una futura versión de API puede romper las expectativas de sus usuarios.

Una buena alternativa es el enfoque adoptado por la característica `generate_read_methods` de ActiveRecord. En lugar de esperar que `method_missing` intercepte las llamadas, ActiveRecord genera una implementación para los métodos setter y reader de los atributos para que puedan ser llamados a través del envío convencional de métodos.

Este es un método poderoso en general, y la naturaleza dinámica de Ruby hace posible escribir métodos que se reemplacen por versiones optimizadas de sí mismos cuando se los llama por primera vez. Esto se usa en el enrutamiento de Rails, que debe ser muy rápido; veremos eso en acción más adelante en este capítulo.

#### Programación Generativa: Escribir código sobre la marcha

Una poderosa técnica que abarca algunos de los otros es la programación generativa, código que escribe código.

Esta técnica puede manifestarse de la manera más simple, como escribir un guión de shell para automatizar una parte tediosa de la programación. Por ejemplo, es posible que desee poblar sus dispositivos de prueba con un proyecto de muestra para cada usuario:

```ruby
brad_project:
    id: 1
    owner_id: 1
    billing_status_id: 12
john_project:
    id: 2
    owner_id: 2
    billing_status_id: 4
...
```

Si se tratara de un lenguaje sin dispositivos de prueba con secuencias de comandos, es posible que los escriba a mano. Esto se complica cuando los datos comienzan a crecer, y es casi imposible cuando los dispositivos tienen dependencias extrañas en los datos de origen. La programación generativa ingenua le obligaría a escribir un guión para generar este accesorio desde la fuente. Aunque no es ideal, esta es una gran mejora sobre escribir los accesorios completos a mano. Pero esto es un dolor de cabeza de mantenimiento: debe incorporar el script en su proceso de compilación y asegurarse de que el dispositivo se regenera cuando cambian los datos de origen.

Esto rara vez, si alguna vez, es necesario en Ruby o Rails \(afortunadamente\). Casi todos los aspectos de la configuración de la aplicación Rails son programables, debido en gran parte al uso de lenguajes específicos de dominio interno \(DSL\). En una DSL interna, usted tiene todo el poder del lenguaje Ruby a su disposición, no solo la interfaz particular que el autor de la biblioteca decidió que debería tener.

Volviendo al ejemplo anterior, ERb hace que nuestro trabajo sea mucho más fácil. Podemos inyectar código Ruby arbitrario en el archivo YAML anterior usando las etiquetas `<% %>` y `<%= %>` de ERb, incluida la lógica que necesitamos:

```ruby
<% User.find_all_by_active(true).each_with_index do |user, i| %>
     <%= user.login %>_project:
       id: <%= i %>
       owner_id: <%= user.id %>
       billing_status_id: <%= user.billing_status.id %>
<% end %>
```



