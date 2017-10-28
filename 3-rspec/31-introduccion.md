# 3.1 Introduccion

El desarrollo impulsado por el comportamiento comenzó su andadura como un intento de comprender mejor y explicar el proceso del desarrollo basado en pruebas. Dan North había observado que los desarrolladores a los que estaba asesorando estaban teniendo dificultades para relacionarse con TDD como herramienta de diseño y llegó a la conclusión de que tenía mucho que ver con la palabra _test_.

Dave Astels llevó eso al siguiente paso en el artículo seminal "Una nueva mirada al desarrollo impulsado por pruebas" 1, en el que sugirió que incluso los TDD experimentados no obtenían todos los beneficios de TDD que podían obtener.

Para poner esto en perspectiva, quizás una breve exploración del Desarrollo Impulsado por Pruebas esté en orden.

### Desarrollo impulsado por pruebas: donde comenzó todo

Test-Driven Development es una práctica para desarrolladores que consiste en escribir pruebas antes de escribir el código que se prueba. Comience escribiendo una prueba muy pequeña para un código que aún no existe. Ejecute la prueba y, naturalmente, falla. Ahora escriba el código suficiente para hacer que pase la prueba. No más.

Una vez que pase la prueba, observe el diseño resultante y refactorice cualquier duplicación que vea2. Es natural en este momento juzgar el diseño como demasiado simple para manejar todas las responsabilidades que este código tendrá.

En lugar de agregar más código, documente la próxima responsabilidad en la forma de la próxima prueba. Ejecútelo, obsérvelo, escriba el código suficiente para que pase, revise el diseño y elimine la duplicación. Ahora agregue la siguiente prueba, mire si falla, haga que pase, refactorice, falle, pase, refactorice, falle, pase, refactor, etc., y así sucesivamente.

En muchos sistemas de pruebas unitarias, cuando falla una prueba, vemos los resultados impresos en rojo. Luego, cuando pasa, los resultados se imprimen en verde. Debido a esto, a menudo nos referimos a este ciclo como rojo / verde / refactor.

#### Diseño emergente

A medida que una base de código aumenta de tamaño, encontramos que el paso de refactorización consume más atención. El diseño está en constante evolución y bajo constante revisión, aunque no está predeterminado.

Este es un diseño emergente a nivel granular y es uno de los subproductos más importantes del desarrollo basado en pruebas.

En lugar de pensar en TDD como una práctica de prueba, lo vemos como una técnica utilizada para entregar códigos de alta calidad a los testers, que son responsables de las prácticas formales de prueba \(consulte ¿Pero qué pasa si "los testers" soy yo?\).

> Joe pregunta:
>
> Pero, ¿y si "el tester" soy yo?
>
> No todos los equipos de proyectos tienen un rol de tester por separado. En los equipos que no lo hacen, la idea de quitar la responsabilidad de probar las prácticas a otras personas realmente no vuela. En casos como este, sigue siendo útil separar las prácticas de prueba de TDD.
>
> **Cuando esté "usando su sombrero TDD", céntrese en rojo / verde / refactor, diseño y documentación. No pienses en las pruebas. Una vez que haya desarrollado un cuerpo de código, póngase su "sombrero de prueba" y piense en todas las cosas que podrían salir mal**. Aquí es donde añades todos los casos extremos, usando pruebas exploratorias para eliminar los desagradables errores que se esconden en las grietas y documentarlos a medida que los descubres con más ejemplos de código.

Y aquí es donde la prueba en TDD se convierte en un problema. Específicamente, es la idea de las pruebas unitarias la que a menudo lleva a los nuevos TDD a verificar cosas tales como asegurarse de que un método de registro \(\) almacena un Registro en la colección de registros del Registro y esa colección es específicamente una Matriz.

Este tipo de detalle en una prueba crea una dependencia en la prueba sobre la estructura interna del objeto que se prueba. Esta dependencia significa que si otros requisitos nos guían para cambiar el Array a un Hash, esta prueba fallará, aunque el comportamiento del objeto no haya cambiado. Esta fragilidad puede hacer que las suites de prueba sean mucho más costosas de mantener y es la razón principal por la que las suites de prueba se ignoran y, en última instancia, se descartan. Entonces, si probar las partes internas de un objeto es contraproducente a largo plazo, ¿en qué deberíamos concentrarnos cuando escribimos estas pruebas primero?

### Desarrollo impulsado por el comportamiento: el siguiente paso

El problema al probar la estructura interna de un objeto es que estamos probando qué es un objeto en lugar de lo que hace. Lo que hace un objeto es significativamente más importante.

Lo mismo es cierto en el nivel de aplicación. A los interesados ​​generalmente no les importa que los datos se mantengan en una base de datos relacional compatible con ANSI. Les importa que esté en "la base de datos", pero incluso entonces, generalmente quieren decir que está almacenado en algún lugar y que pueden recuperarlo.

#### Es todo comportamiento

BDD pone el foco en el comportamiento en lugar de en la estructura, y lo hace en cada nivel de desarrollo. Ya sea que estemos hablando de un objeto que calcula la distancia entre dos ciudades, otro objeto que delega una búsqueda en un servicio de un tercero o una pantalla orientada al usuario que proporciona retroalimentación cuando proporcionamos una entrada no válida, ¡todo es comportamiento!

Una vez que reconocemos esto, cambia la forma en que pensamos acerca de expulsar el código. Comenzamos a pensar más sobre las interacciones entre las personas y los sistemas, o entre los objetos, que sobre la estructura de los objetos.

#### Obtener las palabras correctas

Creemos que la mayoría de los problemas que enfrentan los equipos de desarrollo de software son problemas de comunicación. BDD tiene como objetivo ayudar a la comunicación mediante la simplificación del lenguaje que utilizamos para describir los escenarios en los que se utilizará el software: dado algún contexto, cuando se produce algún evento, entonces espero algún resultado.

Dado, cuando, entonces, la tríada de BDD, son palabras simples que usamos ya sea que hablemos del comportamiento de la aplicación o del objeto. Los analistas de negocio, testers y desarrolladores los entienden fácilmente. Como verá en la Sección 17.8, Given / When / Then, en la página 242 y en todo el libro, estas palabras están incrustadas en el idioma de Cucumber.

### RSpec

RSpec fue creado por Steven Baker en 2005. Steven había oído hablar sobre BDD de Aslak Hellesøy, que había estado trabajando en un proyecto con Dan North cuando la idea salió a la luz por primera vez. Steven ya estaba interesado en la idea cuando Dave Astels sugirió que con lenguajes como Smalltalk y Ruby, podríamos explorar más libremente nuevos marcos de TDD que podrían alentar el enfoque en el comportamiento. Y RSpec nació.

Aunque los detalles sintácticos han evolucionado desde la versión original de Steven de RSpec, la premisa básica permanece. Usamos RSpec para escribir ejemplos ejecutables del comportamiento esperado de un pequeño fragmento de código en un contexto controlado. Así es como podría verse eso:

```ruby
describe MovieList do
  context "when first created" do
    it "is empty" do
      movie_list = MovieList.new
      movie_list.should be_empty
    end 
  end
end
```

El método `it()` crea un ejemplo del comportamiento de una `MovieList`, con el contexto de que se acaba de crear la `MovieList`. La expresión `movie_list.should be_empty` es autoexplicativa. Solo léelo en voz alta. Verá cómo `be_empty()` interactúa con `movie_list` en la Sección 13.3, Predicate Matchers, en la página 163.

Ejecutar este código en un shell con el comando rspec produce la siguiente especificación:

```
MovieList when first created        
is empty
```

Agregue algunos contextos y ejemplos más, y la salida resultante se parece más a una especificación para un objeto `MovieList`.

```
MovieList when first created        
is empty      

MovieList with 1 item        
is not empty        
includes that item
```

Por supuesto, estamos hablando de la especificación de un objeto, no de un sistema. Puede especificar el comportamiento de la aplicación con RSpec. Muchos lo hacen Lo ideal es, sin embargo, para especificar el comportamiento de la aplicación, queremos algo que se comunique en trazos más amplios. Y para eso, usamos Cucumber.

### Cucumber

Como leerá en el Capítulo 11, Escribir software que importa, en la página 121, BDD es una metodología ágil de pila completa. Toma algunas de sus señales de la Programación Extrema, que incluye una variación del Desarrollo Impulsado por la Prueba de Aceptación llamado Planificación Basada en la Prueba de Aceptación \(ATDP\).

En ATDP, utilizamos pruebas de aceptación del cliente para impulsar el desarrollo del código. Idealmente, estos son el resultado de un esfuerzo de colaboración entre el cliente y el equipo de entrega. Algunas veces son escritos por el equipo de entrega y luego revisados / aprobados por el cliente. En cualquier caso, están orientados al cliente y deben expresarse en un idioma y formato con los que los clientes puedan identificarse. Cucumber nos da ese lenguaje y formato.

Cucumber lee descripciones de texto sin formato de las características de la aplicación con escenarios de ejemplo y utiliza los pasos del escenario para automatizar la interacción con el código que se está desarrollando. Aquí hay un ejemplo:

![](/assets/Captura de pantalla 2017-10-28 a las 8.37.19 a.m..png)

> Semillas de Cucumber
>
> Incluso antes de comenzar a explorar estructuras y sintaxis para RSpec, Dan North había estado explorando un modelo completamente diferente para una herramienta BDD.
>
> Quería documentar e impulsar el comportamiento en un lenguaje simplificado que los clientes, desarrolladores, probadores, analistas de negocios, etc., pudieran entender fácilmente. El resultado inicial de esa exploración fue la biblioteca JBehave, que todavía está en uso y desarrollo activo.
>
> Dan transfirió a JBehave a Ruby como RBehave, y lo fusionamos en RSpec como Story Runner. Al principio solo admitía escenarios escritos en Ruby, pero luego agregamos soporte para texto sin formato, abriendo un nuevo mundo de expresividad y acceso. Pero a medida que se revelaron nuevas posibilidades, también lo fueron las limitaciones.
>
> En la primavera de 2008, Aslak Hellesøy se propuso reescribir el Story Runner de RSpec con una gramática real definida con la biblioteca Treetop de Nathan Sobo. Aslak lo llamó Cucumber por sugerencia de su prometida, Patricia Carrier, pensando que sería un título de trabajo efímero hasta que se fusionara de nuevo en RSpec. Poco sabían ellos que Cucumber desarrollaría una vida propia.

Todo hasta e incluyendo la declaración de escenario en la línea 7 se trata como documentación \(no ejecutable\). Las líneas siguientes son pasos en el escenario.

En el Capítulo 4, Automatización de funciones con Cucumber, en la página 35, estará escribiendo definiciones de pasos en Ruby. Estas definiciones de paso interactúan con el código que se está desarrollando y son invocadas por Cucumber tal como se lee en el escenario.

No te preocupes si eso todavía no tiene sentido para ti. Por ahora, solo es importante entender que tanto RSpec como Cucumber nos ayudan a especificar el comportamiento del código con ejemplos que están vinculados programáticamente a ese código. Los detalles se aclararán a medida que sigas leyendo.

Usamos Cucumber para describir el comportamiento de las aplicaciones y usamos RSpec para describir el comportamiento de los objetos.3 Si alguna vez ha hecho TDD antes, probablemente esté familiarizado con el ciclo rojo / verde / refactor. Con la adición de una herramienta de nivel superior como Cucumber, en realidad tendremos dos ciclos concéntricos rojo / verde / refactor, como se muestra en la Figura 1, El ciclo BDD, en la página 9.

Ambos ciclos implican tomar pequeños pasos y escuchar los comentarios que recibe de las herramientas. Comenzamos con un paso fallido \(rojo\) en Cucumber \(el ciclo externo\). Para que ese paso pase, bajaremos a RSpec \(el ciclo interno\) y eliminaremos el código subyacente a nivel granular \(rojo / verde / refactor\).

### El ciclo de BDD

En cada punto verde en el ciclo de RSpec, verificaremos el ciclo de Cucumber. Si todavía está rojo, la retroalimentación resultante debería guiarnos a la siguiente acción en el ciclo RSpec. Si es verde, podemos saltar a Cucumber, refactorizar si es apropiado y luego repetir el ciclo escribiendo un nuevo paso de Cucumber que falla.

En el siguiente capítulo, lo configuraremos con Cucumber y RSpec y lo guiaremos a través de un simple ejemplo de cada herramienta. En el tutorial que comienza en el Capítulo 3, Describiendo características, en la página 19, usaremos una serie de características en Cucumber y RSpec. En la mayoría de los casos, solo tocaremos la superficie de una característica, cubriendo lo suficiente como para poder usarla según sea necesario para este proyecto, con referencias a otros lugares en el libro donde puede obtener más información sobre los detalles y la filosofía detrás de cada característica.

![](/assets/Captura de pantalla 2017-10-28 a las 8.48.29 a.m..png)





