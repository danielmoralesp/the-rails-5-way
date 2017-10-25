# 2.2 - ActiveSupport y RailTies

Continuamos en nuestra vista ascendente de Rails examinando las piezas que forman la base de Rails. ActiveSupport es una biblioteca que proporciona funciones genéricas y reutilizables que no son específicas de ninguna parte de Rails. Podemos usar muchos de estos métodos nosotros mismos al escribir nuestro código de aplicación. RailTies es la otra mitad, que contiene piezas que unen rails de una manera específica. Aunque normalmente no usaremos las funciones de Rail- Ties en nuestro propio código, es importante e instructivo examinarlas.

La mayor parte de este capítulo no es secuencial; siéntete libre de saltearte. Sin embargo, de acuerdo con nuestro enfoque ascendente de Rails, los capítulos posteriores se basarán en este material.

### Ruby puede haberse perdido

Es muy fácil pasar por alto algunos de los métodos más útiles de Ruby. La mejor forma de encontrarlos es leer el código. Estos son algunos de los más oscuros, pero útiles.

### Cómo leer el código

Tal como lo sugiere la cita que presenta este capítulo, el propósito principal del código fuente no debe ser expresar la implementación a una computadora; debería estar expresando el significado a las personas. Los lenguajes de programación son un medio increíblemente expresivo y escueto para los conceptos de los que hablan los programadores. Las propuestas para hacer que los lenguajes de programación sean más parecidos a los ingleses inevitablemente fracasan no por una implementación deficiente sino porque existe un desajuste de impedancia inherente entre los dominios del idioma inglés y la programación de computadoras.

Por lo tanto, los lenguajes de programación deben compararse no por sus niveles de potencia bruta \(cualquier lenguaje de Turing completo satisface trivialmente este requisito\) o la velocidad de ejecución \(para la mayoría de las aplicaciones, la velocidad no es crítica\) sino por su eficiencia del programador: la velocidad a lo que un programador puede traducir con precisión sus pensamientos en código.

Estrechamente relacionada con la eficiencia del programador está la eficiencia del mantenedor: la capacidad de un mantenedor \(que puede ser el desarrollador original, 12 meses después\) para leer el código y deducir lo que está sucediendo. Perl a menudo es criticado por ser "solo de escritura"; es fácil escribir código que es casi ilegible para futuros desarrolladores. Tal código tendría una alta eficiencia del programador a costa de la eficiencia del mantenedor. \*

Ruby gana en ambos frentes: la mayoría del código de Ruby es fácil de escribir y leer, una vez que conoces la sintaxis y la semántica básicas. † Sin embargo, sumergirte en cualquier proyecto grande como Rails es difícil. Aquí, discutimos formas de comenzar a leer una base de código.

### Cómo ubicar el código

Una desventaja de la naturaleza dinámica de Ruby es que hay pocas oportunidades para la reflexión del tiempo de desarrollo sobre el código de Ruby. Cuando se usa un lenguaje más estático, los IDE pueden inferir el tipo de variables, y de ahí deducir los métodos disponibles para esas variables. Por lo tanto, pueden ofrecer ayuda en la codificación al sugerir nombres de variables y métodos. En Ruby, en principio, la única forma de conocer los métodos disponibles para un objeto es evaluar la expresión que devuelve el valor de ese objeto. Esto es claramente impráctico debido a los efectos secundarios de la evaluación o las diferencias en el entorno de desarrollo y ejecución. El efecto es que es imposible escribir un IDE de estilo de finalización de código general para Ruby.

En la práctica, esto generalmente no es un problema. Ruby sigue un estilo de desarrollo que está más cerca de Lisp que de C/C++. Los desarrolladores interactúan con su aplicación al crearla, y generalmente responden preguntas sobre el estado del sistema haciéndole preguntas mientras se está ejecutando. Sin embargo, existen algunos métodos para buscar información mediante el examen estático del código:

• Un buen editor de texto lo ayudará a eliminar gran cantidad de fuentes.

- TextMate \(http://macromates.com/\) es el editor semioficial del equipo central de Ruby on Rails. Tiene excelentes instalaciones para búsqueda \(incluida la búsqueda por expresiones regulares\) y viene con algunas características bastante impresionantes de Ruby and Rails. Es compatible con proyectos \(árboles fuente completos administrados como una unidad\). Sin embargo, está disponible solo para Mac OS X y cuesta 39 en el momento de escribir este artículo.

- Vim \(http://vim.org/\) es un increíble editor de texto de código abierto disponible para casi cualquier plataforma. Tiene una larga curva de aprendizaje, pero es extremadamente poderosa. Si usa Vim con Rails, hágase un favor e instale el paquete vim-ruby \(http://rubyforge.org/projects/vim-ruby/\).

• Junto con un buen editor de texto, debe familiarizarse con las herramientas de línea de comandos para el procesamiento de texto. Las expresiones regulares \(usadas con la herramienta de su elección, como sed, Perl o egrep\) proporcionan un lenguaje de consulta más poderoso para encontrar patrones dentro de grandes cuerpos de fuente.

• SearchtheWeb. La popularidad de Rails ha creado una gran cantidad de bloggers que muestran guiones sobre Ruby and Rails, y generalmente llenan los vacíos donde falta la documentación oficial. Google Code Search \* indexa miles de proyectos de código abierto y tiene funciones útiles, como buscar por expresión regular.

### Leyendo el call stack

La mayoría de los desarrolladores de Rails están familiarizados con la lectura de un seguimiento de pila al depurar excepciones. Puede ser muy útil conocer la secuencia en la que se invocaron las funciones de marco y aplicación cuando algo sale mal. Pero, ¿y si tenemos curiosidad? No tiene sentido presentar una excepción solo para generar un trazo retrospectivo legible. Afortunadamente, tenemos algunas herramientas a nuestra disposición para analizar la pila de llamadas en código de ejecución, de forma no destructiva.

La primera herramienta de este tipo es el método `Kernel#caller` estándar de Ruby, que nos proporciona una traza inversa simple como una matriz de cadenas:

### Depuración de ruby and rails

Ruby se envía con un depurador incorporado, rdebug. Sin embargo, Kent Sibilev ha mejorado esto y ha lanzado su propia biblioteca de depuración de Ruby, Ruby-Debug. Este es un depurador de Ruby con todas las funciones que también incluye soporte de punto de interrupción para Rails.

Rails solía tener soporte de depuración incorporado, basado en `Binding.of_ caller`, que explotaba un error de Ruby 1.8.4. Cuando el error se solucionó en Ruby 1.8.5, el puntero se rompió y tuvimos que recurrir a utilidades de terceros para depurar las aplicaciones de Rails.

Para comenzar a usar ruby-debug, instálalo con gem:

`sudo gem install ruby-debug`

### Donde empezar

Elige algo interesante

No descarte la importancia de encontrar algo que le guste y correr con él. Este enfoque tiene varias ventajas. Lo mantiene mirando los aspectos que le interesan, mientras lo lleva a través de los componentes básicos, como ActiveSupport, que necesitará saber. También aprenderá los modismos que usan otros programadores de Rails, y probablemente aprenderá algo nuevo en el proceso.

Aprende a amar la búsqueda global. Encuentre un método interesante y busque Rails para todos los lugares a los que se llama ese método.

Comience en la parte superior

El final de este capítulo explicará cómo funciona la inicialización de Rails. Estas funciones proporcionan buenos puntos de entrada para estudiar la fuente Rails. Puede comenzar desde Initializer y rastrear una solicitud a través de una secuencia de llamadas a su aplicación, siguiendo el mismo camino que Ruby cuando ejecuta su código.

