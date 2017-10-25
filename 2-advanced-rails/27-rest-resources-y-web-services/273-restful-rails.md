# 2.7.3 - RESTful Rails

En RailsConf 2006, el discurso principal de David Heinemeier Hansson marcó el comienzo de la filosofía RESTful que se convirtió en la corriente principal en Rails. El discurso principal, Descubriendo un mundo de recursos en rails, \* presentó una hoja de ruta para mover Rails 1.2 hacia un valor predeterminado más RESTful, basado en CRUD.

Uno de los puntos clave en la presentación fue que los recursos pueden ser más de lo que podríamos pensar como "objetos"; ejemplos dados fueron relaciones entre objetos, eventos en objetos y estados de objeto. Este es un principio importante en REST. En lugar de agregar otro método \#close en un objeto Case, puede ser más claro factorizar un objeto Closure si necesita persistir más información sobre el cierre.

### Enrutamiento RESTful

Desde el exterior de una aplicación, el cambio más visible en RESTful Rails es el nuevo enrutamiento. El enrutamiento de los rieles clásicos se basaba en la ruta predeterminada de /: controller /: action /: id, con los parámetros adicionales que generalmente se llevan en la cadena de consulta. Esto tenía ventajas de simplicidad y uniformidad en el enrutamiento, pero era frágil cambiar. Refactorizar acciones de un controlador a otro requería actualizar todos los enlaces que apuntaban a esa acción; tenían que ser cambiados de:

### ActiveResource: consumir servicios RESTful

Una gran ventaja de codificar servicios web RESTfully es que los clientes pueden con- sumirlos utilizando una interfaz estándar. ActiveResource es una biblioteca, ahora parte de edge Rails, que abstrae recursos RESTful utilizando una interfaz similar a ActiveRecord. Se adapta bien al modelo Rails de creación de aplicaciones, y siempre que el servidor se haya construido utilizando convenciones RESTful Rails, el uso de ActiveResource es casi transparente.

Para demostrar el poder de ActiveResource, crearemos una aplicación muy simple \(nada más que una interfaz con andamios para un conjunto de objetos ActiveRecord\) para administrar un conjunto de productos en una tienda ficticia. Primero crearemos esto como una aplicación básica de ActiveRecord utilizando los principios RESTful; luego usaremos ActiveResource para desconectar el servicio web back-end de la interfaz front-end.

