# 2.7.1 - ¿Qué es REST?

En resumen, REST es una teoría unificadora de cómo los sistemas "hipermedia distribuidos" \(principalmente la World Wide Web\) están mejor organizados y estructurados. El término fue acuñado por RayFielding, coautor de la especificación HTTTP, en su disección de 2000 aspectos arquitectónicos y el diseño de arquitecturas de software basadas en red. \* La disertación extrae un conjunto de principios que son comunes a las arquitecturas de red, basados en un examen de la estructura del protocoloWebtheHTTP. Comenzando con el "estilo nulo", que es la ausencia de restricciones en la arquitectura, Fielding llega a REST colocando una serie de restricciones en la arquitectura de la red:

### Servidor de cliente

La restricción cliente-servidor impone una separación del almacenamiento de datos de la interfaz del usuario y la presentación. El beneficio más importante de esta separación es que el cliente y el servidor pueden existir en organizaciones separadas y mantenerse, desarrollarse y escalarse de manera independiente.

### Stateless

El servidor no puede tener un estado persistente sobre sus sesiones con el cliente. Cada solicitud del cliente al servidor es independiente y autónoma. Esto aumenta la verbosidad, pero ayuda a la confiabilidad y la escalabilidad. Cuando hay poco o ningún contexto almacenado en un servidor, el sistema es más resistente a fallas periódicas, y hay menos requisitos para la coordinación entre servidores cuando se escala el sistema.

### Cache

Este paso requiere que el servidor indique si el cliente puede almacenar o no una respuesta en caché y definir parámetros para dicho almacenamiento en caché. Proporcionar información de control de caché explícita permite al cliente almacenar en caché de forma más agresiva, reduciendo el tráfico de la red y aumentando el rendimiento.

### Interfaz uniforme

Una interfaz uniforme es el elemento principal que distingue REST de RPC y otros estilos de red. Forzar al cliente y al servidor a comunicarse utilizando una interfaz uniforme bien conocida empuja la complejidad específica de la aplicación fuera de la capa de red hacia la capa de aplicación, donde pertenece. Permite reutilizar componentes de software estandarizados para aplicaciones muy diferentes, ya que hablan el mismo idioma.

### Sistema de capas

Las arquitecturas se pueden dividir en capas independientes \(en el sentido de los modelos en capas OSI o TCP / IP\). Cada capa media solo entre las capas inmediatamente adyacentes a ella. De esta forma, las capas pueden evolucionar o ser reemplazadas de forma independiente con un impacto mínimo en el resto de la arquitectura.

### Code-on-Demand

El software del cliente es extensible al recuperar y ejecutar el código de un servidor. Esto permite a los clientes expandir sus capacidades de una manera ad-hoc después de desplegar una arquitectura.

REST a menudo se conoce como un estilo arquitectónico, en lugar de una arquitectura. En lugar de definir una "mejor" arquitectura específica, REST define los principios mediante los cuales se crean y evalúan las arquitecturas: impone restricciones a la arquitectura de la red.

A menudo usamos una analogía lingüística para explicar REST; en muchos sentidos, los principios de REST se basan en la comunicación humana. Los nombres de recursos son "sustantivos", ya que se refieren a cosas. Es importante recordar la distinción entre los recursos y sus nombres. Así como la palabra "manzana" no es en sí misma una manzana, el nombre http: // example.com/person/123 es solo un nombre, no el recurso en sí. Un corolario es que un recurso puede tener muchos nombres \(aunque un buen estilo REST indica que esto debe mantenerse al mínimo, cuando sea posible\).

En el proceso, los métodos HTTP se denominan "verbos" porque especifican una acción en un recurso o su representación.

La tesis de Fielding es muy general; en realidad se aplica a cualquier "arquitectura basada en red". Sin embargo, REST se aplica comúnmente a la World Wide Web y HTTP, y restringiremos nuestro tema de discusión en este capítulo a la aplicación de REST a HTTP. Necesariamente, esto impondrá restricciones y directrices no derivadas de la tesis de Fielding. Por lo tanto, los principios explicados en este capítulo son un subconjunto de los del RESTO de Fielding.

En cualquier caso, REST simplemente explicó "HTTPasitwasmeanttobe". A medida que aumentaba la popularidad, muchas de las decisiones de diseño originales que guiaron aHTTP fueron ignoradas. Los desarrolladores de aplicaciones de Internet pretendían cosas como los códigos de estado de respuesta y de respuesta de HTP como incidentales a la aplicación, o como una trivialidad a ser tratada cuando se les permite el tiempo. El uso de HTPT como objetivo fue considerado demasiado innecesario. Sin embargo, en años recientes, los resultados de los REST indican que el HTTP era lo suficientemente bueno después de todo. Los desarrolladores están aprendiendo estas lecciones:

• La mayoría, si no todos, los dominios pueden ser fácilmente modelados fácilmente como conjuntos de operaciones CRUD \(crear, leer, actualizar, eliminar\). Estas operaciones corresponden aproximadamente a HTTP POST, GET, PUT y DELETE, respectivamente. De esta manera, el conjunto de acciones está estandarizado.

• Los nombres correspondientes a los recursos \(/ persona / 123\) son en general consistentes, sólidos y comprensibles. Los nombres correspondientes a los puntos finales del servicio \(/ personService\) tienden a ser demasiado amplios y poco específicos, mientras que los nombres correspondientes a las llamadas RPC \(/ person / show / 123, cuando se accede con GET\) son redundantes.

• Las claves de ortogonalidad. Los nombres deben identificar los recursos, nada más ni nada menos. Las consecuencias y las fallas deben recibirse del estadístico de respuesta de HTTTP, no de un mensaje de error dentro de la carga útil. Un recurso puede tener múltiples representaciones, pero todas deben identificarse como provenientes del mismo recurso \(y sus nombres deben reflejar eso\).

La combinación de sustantivos, verbos y tipos de contenido a menudo se denomina triángulo REST. Juntos, las tres esquinas del triángulo definen la arquitectura. Una descomposición de diseño REST-céntrica a menudo se hace al decidir sobre los sustantivos \(identificar y nombrar cosas\), seleccionar un conjunto uniforme de verbos \(fácil si usa HTTP\) y elegir tipos de contenido.

### Verbos

Los verbos corresponden a acciones sobre recursos. Un verbo enviará una representación de un recurso desde el servidor al cliente o actualizará el recurso en el servidor con información del cliente.

En REST, los verbos son un territorio restringido. Mientras que el conjunto de tipos de contenido está abierto para revisión y expansión, y los nombres de recursos son infinitamente ampliables, el conjunto de verbos es fijo. Sin embargo, las restricciones puestas en el alcance de los verbos les permiten ser universales; cualquier verbo se puede aplicar a cualquier sustantivo.

HTTPdefinesahandfulmethods; thesetcanexpanded byprotocolssucha WebDAV, pero el conjunto básico es suficiente para REST. Los cuatro métodos más comunes son GET, PUT, DELETE y POST; vamos a examinar cada uno de ellos y su propósito aquí.

Podemos formar algunas analogías lingüísticas como una simplificación de los cuatro verbos comunes. Hablando en términos generales, esto es lo que quieren decir, usar "esto" para referirse al cuerpo de la solicitud y "allí" para referirse a la URI sobre la que se actúa:

• GET: "Dame lo que sea que esté allí".

• PUT: "Almacenar esto allí".

• ELIMINAR: "Eliminar lo que esté allí".

• POST: "Oye tú, procesa esto".

### Recursos

El concepto más fundamental de REST es el recurso. La definición más general de un recurso es algo con identidad. Es tan simple como eso. En el uso popular, el término "recurso" generalmente significa algo que es direccionable por red en Internet, y es con este tipo de recursos que nos ocuparemos. Pero un recurso puede ser cualquier cosa, tangible o intangible, que pueda nombrarse. Como RFC 2396 explica: \*

Un recurso puede ser cualquier cosa que tenga identidad. Los ejemplos conocidos incluyen un documento electrónico, una imagen, un servicio \(por ejemplo, "informe meteorológico de hoy para Los Ángeles"\) y una colección de otros recursos. No todos los recursos son red "recuperables"; Por ejemplo, los seres humanos, las empresas y los libros encuadernados en una biblioteca también pueden considerarse recursos.





