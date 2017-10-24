# 2.1 - Técnicas Fundamentales

Desde su lanzamiento inicial en julio de 2004, el framework web Ruby on Rails ha ganado popularidad constantemente. Rails ha estado convirtiendo a los desarrolladores de PHP, Java y .NET de una manera más simple: una arquitectura modelo-vista-controlador \(MVC\), valores predeterminados razonables \("convención sobre configuración"\) y el poderoso lenguaje de programación Ruby.

Rails tenía una mala reputación por la falta de documentación durante su primer año o dos. Esta brecha ha sido completada por los miles de desarrolladores que usan, contribuyen y escriben sobre Ruby on Rails, así como también por el proyecto de documentación de Rails \([http://railsdocumentation.org/\](http://railsdocumentation.org/%29\). Hay cientos de blogs que ofrecen tutoriales y consejos para el desarrollo de Rails.

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

La implementación de ActiveRecord de este práctico truco no podría ser más simple:

```ruby
yaml = YAML::load(erb_render(yaml_string))
```

usando el helper method `erb_render`

```ruby
def erb_render(fixture_content)
       ERB.new(fixture_content).result
end
```

La programación generativa a menudo usa Module Module `define_method` o `class_eval` y `def` para crear métodos sobre la marcha. ActiveRecord usa esta técnica para acceder a los atributos; la característica `generate_read_methods` define los métodos setter y reader como métodos de instancia en la clase ActiveRecord para reducir el número de veces que se necesita `method_missing` \(una técnica relativamente costosa\).

### Continuaciones

Las continuaciones son un mecanismo de control de flujo muy poderoso. Una continuación representa un estado particular de la pila de llamadas y las variables léxicas. Es una instantánea de un punto en el tiempo al evaluar el código de Ruby. Desafortunadamente, la implementación de continuaciones de Ruby 1.8 es tan lenta que no se puede usar en muchas aplicaciones. Las próximas máquinas virtuales Ruby 1.9 pueden mejorar esta situación, pero no debe esperar un buen rendimiento de las continuaciones bajo Ruby 1.8. Sin embargo, son construcciones útiles, y los marcos web basados ​​en la continuación proporcionan una alternativa interesante a los marcos como Rails, por lo que estudiaremos su uso aquí.

Las continuaciones son poderosas por varias razones:

• Las continuaciones son solo objetos; se pueden pasar de una función a otra.

• Las continuaciones pueden invocarse desde cualquier lugar. Si mantiene una referencia a una continuación, puede invocarla.

• Las continuaciones son reingresantes. Puede usar continuaciones para regresar de una función varias veces.

Las continuaciones a menudo se describen como "GOTO estructurado". Como tales, deben tratarse con la misma precaución que cualquier tipo de construcción GOTO. Las continuaciones tienen poco o ningún lugar dentro del código de la aplicación; por lo general, deben estar encapsulados dentro de las bibliotecas. No digo esto porque creo que los desarrolladores deberían estar protegidos de ellos mismos. Por el contrario, las continuaciones son lo suficientemente generales como para que tenga más sentido construir abstracciones a su alrededor que usarlas directamente. La idea es que un programador piense en "iterador externo" o "coroutine" \(ambas abstracciones creadas a partir de las continuidades\) en lugar de "continuación" al crear el software de la aplicación.

Seaside \* es un framework de aplicaciones web Smalltalk construido sobre las continuidades. Las continuaciones se usan en Seaside para administrar el estado de la sesión. Cada sesión de usuario corresponde a una continuación del lado del servidor. Cuando entra una solicitud, se invoca la continuación y se ejecuta más código. El resultado es que transacciones enteras se pueden escribir como una secuencia de código en gran escala, incluso para las múltiples solicitudes HTTP. Esta potencia proviene del hecho de que las continuidades de Smalltalk son serializables; se pueden escribir en una base de datos o en el sistema de archivos, luego se pueden descongelar y reinvocar a petición. Las continuaciones de Ruby son no-serializables. En Ruby, las continuaciones están en memoria solamente y no se pueden transformar en una secuencia de bytes.

Borges \(http://borges.rubyforge.org/\) es un puerto directo de Seaside 2 a Ruby. La principal diferencia entre Seaside y Borges es que Borges debe almacenar todas las continuaciones actuales en la memoria, ya que no son serializables. Esta es una gran limitación que, lamentablemente, impide que Borges sea exitoso para aplicaciones web con cualquier tipo de volumen. Si las implementaciones serializables se implementan en una de las implementaciones de Ruby, esta limitación se puede eliminar.

El poder de las continuaciones es evidente en el siguiente código de ejemplo de Borges, que muestra una lista de artículos de una tienda en línea:

```ruby
class SushiNet::StoreItemList < Borges::Component
    def choose(item)
        call SushiNet::StoreItemView.new(item)
    end
    def initialize(items)
        @batcher = Borges::BatchedList.new items, 8
    end
    def render_content_on(r)
        r.list_do @batcher.batch do |item|
            r.anchor item.title do choose item end
        end
        r.render @batcher
    end
end # class SushiNet::StoreItemList
```

La mayor parte de la acción ocurre en el método `render_content_on`, que usa una BatchedList \(un paginador\) para generar una lista paginada de enlaces a los productos. Pero la diversión ocurre en la llamada al anclaje, que almacena la llamada a elegir, para ser ejecutada cuando se hace clic en el enlace correspondiente.

Sin embargo, todavía hay gran desacuerdo sobre cuán útiles son las continuaciones para la programación web. HTTP fue diseñado como un protocolo sin estado, y las continuas para las transacciones web son lo opuesto a la apatridia. Todas las continuaciones se deben almacenar en el servidor, lo que requiere memoria adicional y espacio en el disco. Se requieren sesiones fijas para dirigir el tráfico de un usuario al mismo servidor. Como resultado, si un servidor se cae, todas sus sesiones se pierden. La aplicación más popular de Seaside, DabbleDB \(http://dabbledb.com/\), en realidad usa continuaciones muy poco.

### Vinculaciones

Los enlaces proporcionan contexto para la evaluación del código de Ruby. Un enlace es el conjunto de variables y métodos que están disponibles en un punto particular \(léxico\) en el código. Cualquier lugar en el código de Ruby donde se puedan evaluar las declaraciones tiene una vinculación, y esa vinculación se puede obtener con el enlace Núcleo \#. Los enlaces son solo objetos de la vinculación de clase, y se pueden pasar como cualquier objeto puede:

### Monkeypatching

En Ruby, todas las clases están abiertas. Cualquier objeto o clase es un juego justo para ser modificado en cualquier momento. Esto brinda muchas oportunidades para ampliar o anular la funcionalidad existente. Esta extensión se puede hacer muy limpiamente, sin modificar las definiciones originales.

Rails aprovecha ampliamente el sistema de clases abierto de Ruby. Abrir clases y agregar código se conoce como monkeypatching \(un término de la comunidad de Python\). Aunque suene derogatorio, este término se usa en una luz decididamente positiva; el parche de mono es, en general, visto como una técnica increíblemente útil. Casi todos los plugins de Rails montan el núcleo de Rails de una forma u otra.

#### Desventajas de monkeypatching

Hay dos desventajas principales para monkeypatching. En primer lugar, el código para una llamada a un método puede repartirse en varios archivos. El mejor ejemplo de esto es el método de proceso de ActionController. Este método es interceptado por métodos en hasta cinco archivos diferentes durante el transcurso de una solicitud. Cada uno de estos métodos agrega otra característica: filtros, rescate de excepción, componentes y administración de sesión. El resultado final es una ganancia neta: el beneficio obtenido al separar cada componente funcional en un archivo separado supera la pila de llamadas inflada.

Otra consecuencia de la dispersión de la funcionalidad es que puede ser difícil documentar adecuadamente un método. Debido a que la función del método de proceso puede cambiar dependiendo de qué código se haya cargado, no hay un buen lugar para documentar lo que cada uno de los métodos está agregando. Este problema existe porque la identidad real del método de proceso cambia a medida que los métodos se encadenan entre sí.

### Agregar funcionalidad a los métodos existentes

Debido a que Rails fomenta la filosofía de la separación de concerns, a menudo tendrá la necesidad de ampliar la funcionalidad del código existente. Muchas veces querrá "aplicar parches" a una función existente sin alterar el código de esa función. Su adición puede no estar directamente relacionada con el propósito original de la función: puede agregar autenticación, registro o alguna otra preocupación transversal importante.

Examinaremos varios enfoques del problema de las preocupaciones transversales y explicaremos el \(encadenamiento de métodos\) que ha adquirido mayor impulso en las comunidades de Ruby and Rails.

#### Subclasificación

En la programación tradicional orientada a objetos, una clase se puede extender heredando de ella y cambiando sus datos o comportamiento. Este paradigma funciona para muchos propósitos, pero tiene inconvenientes:

• Los cambios que desea realizar pueden ser pequeños, en cuyo caso la configuración de una nueva clase puede ser demasiado compleja. Cada nueva clase en una jerarquía de herencia se suma a la sobrecarga mental requerida para comprender el código.

• Es posible que deba realizar una serie de cambios relacionados a varias clases que, por lo demás, no están relacionadas. Subclasificar cada uno individualmente sería excesivo y separaría la funcionalidad que debería mantenerse junta.

• Es posible que la clase ya esté en uso en toda la aplicación y desee cambiar su comportamiento de forma global.

• Es posible que desee agregar o quitar una función en el tiempo de ejecución y hacer que surta efecto de manera global. \(Exploraremos esta técnica con un ejemplo completo más adelante en el capítulo\).

En lenguajes orientados a objetos más tradicionales, estas características requerirían código complejo. El código no solo sería complejo, sino que estaría estrechamente vinculado al código existente o al código que lo llama.

#### Método de encadenamiento

La solución estándar de Ruby para este problema es el encadenamiento de métodos: aliasing un método existente a un nuevo nombre y sobrescribir su definición anterior con un nuevo cuerpo. Este nuevo cuerpo usualmente llama a la vieja definición de método al referirse al nombre con alias \(el equivalente a llamar a super en un método de sustitución heredado\). El efecto es que una característica se puede parchear alrededor de un método existente. Debido a la naturaleza de clase abierta de Ruby, las características se pueden agregar a casi cualquier código desde cualquier lugar. Huelga decir que esto debe hacerse con prudencia a fin de mantener la claridad.



