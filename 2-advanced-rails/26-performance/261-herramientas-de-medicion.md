# 2.6.1 - Herramientas de medición

Por supuesto, para medir adecuadamente el rendimiento, necesitamos herramientas. Esta sección se refiere al análisis del código Ruby and Rails, así como a las aplicaciones web en general. Hay una serie de herramientas que se pueden utilizar para analizar la pila completa de Rails, desde HTTP hasta las partes internas de Ruby.

### Análisis Black-Box

La medición más básica de alto nivel que le interesará es: en el caso ideal, ¿con qué rapidez puede atender este servidor? Si bien la respuesta es un valor algo nebuloso que a menudo no guarda relación con el rendimiento real bajo una carga típica, sigue siendo útil compararlo consigo mismo, por ejemplo, cuando se prueba el almacenamiento en caché o la implementación de un nuevo conjunto de características.

Esta técnica se llama análisis de recuadro negro: medimos cuánto tráfico puede manejar el servidor, mientras lo tratamos como una "caja negra". Por ahora, realmente no nos importa lo que hay dentro de la caja, solo acerca de qué tan rápido puede servir. peticiones. Dejaremos las minucias de los perfiles y ajustes hasta más tarde.

Para esta etapa, necesitaremos una utilidad de evaluación comparativa, pero primero, una breve desviación hacia el mundo de las matemáticas.

### Code Timing

La biblioteca estándar de Ruby incluye Benchmark, que se puede utilizar para responder preguntas simples sobre el rendimiento del código. La palabra clave aquí es simple: es demasiado fácil ignorar los factores de confusión y tomar los números que Benchmark te da como evangelio.

### Rails Analyzer Tools

Las herramientas de Rails Analyzer \(http://rails-analyzer.rubyforge.org/\) son un conjunto de utilidades que pueden ayudar a perfilar su aplicación Rails. Mientras que Benchmark es específico de Ruby, y httperf comparará cualquier sitio web, las herramientas de Rails Analyzer se escribieron teniendo en cuenta a Rails.

#### Analizador de registro de producción

La primera herramienta, Production Log Analyzer, analiza los archivos de registro de producción para encontrar las acciones más lentas en la aplicación implementada. Esto es muy útil para la resolución de problemas, pero tiene varias desventajas. Requiere que los registros pasen por SyslogLogger \(provisto con Rails Analyzer Tools\), por lo que debe configurarlo antes de que entren las solicitudes. La herramienta también requiere un syslogd que admita el filtrado de nombres de programas, lo que generalmente significa ejecutar BSD o instalando syslog-ng. Por estas razones, no vamos a entrar en detalles sobre su uso aquí. Las instrucciones de instalación completas están disponibles en http://rails-analyzer.rubyforge.org/pl\_analyze/.

#### Analizador de acciones

Una vez que sepa qué acciones están perjudicando el rendimiento, necesita una forma de profundizar. Action Profiler es una biblioteca \(y un ejecutable correspondiente, action\_profiler\) que pega Rails a un generador de perfiles. Perfila una sola acción de Rails a través de toda la pila de Rails y el código de la aplicación para que pueda examinar dónde se está gastando más tiempo durante esa acción.



