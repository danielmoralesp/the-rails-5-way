# 2- Active Job

El punto principal de la estructura de trabajo activo es garantizar que todas las aplicaciones de Rails tengan una infraestructura de trabajo consistente en su lugar. De esta forma, otras características de Rails y gemas de terceros tienen una base estandarizada sobre la cual se puede construir, sin preocuparse por las diferencias API entre las implementaciones de corredores de trabajos, como Delayed Job y Resque. \(Esa fue exactamente la situación anterior a la presentación de Active Job en Rails 4.2, y fue doloroso\).

Active Job funciona "fuera de la caja" con un grupo de subprocesos en proceso simple. El problema principal es que los trabajos en la cola se descartarán si se reinicia el servidor. Para una operación más a prueba de balas, querrá usar una de las bibliotecas de procesamiento de fondo que se tratarán más adelante en este capítulo. Discutiremos las fortalezas y debilidades de cada una de ellas para que pueda determinar qué es lo apropiado para su aplicación. Sin embargo, desde la introducción de Active Job en Rails 4.2, esta decisión es más una preocupación operativa que otra cosa.

### Creando un job

Esta sección describe cómo crear un job y colocarlo en una cola para su procesamiento. Los objetos de active jobs se pueden definir creando una clase que hereda de la clase `ActiveJob::Base`. El único método necesario para implementar es el método de ejecución. Sin embargo, la mayoría de las veces usa un generador de Rails para comenzar desde una clase repetitiva.

### Generador de jobs

Usemos ese generador para crear un job en la  `application/jobs` junto con las especificaciones correspondientes. El trabajo del trabajo \(¡ja!\) Será eliminar las subastas que han terminado hace más de una hora:



