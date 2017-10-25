# 2.7.2 - Beneficios de una arquitectura RESTful

En este capítulo, hemos abordado algunos de los beneficios que una arquitectura de aplicaciones RESTful puede proporcionar, y esperamos que haya visto algunos de esos beneficios para usted. Ahora enumeraremos y explicaremos cada uno de los principales beneficios que REST se esfuerza por lograr.

### Simplicidad conceptual

La piedra angular de REST es la simplicidad. La decisión de usar un conjunto estándar de verbos \(cuando el HTPTverbsordealosotrosetiquetas\) virtualmenteeliminaaúnunaareadediscusión. El sistema uniforme de registro y nombramiento de los tipos MIME ciertamente no resuelve el debate, pero definitivamente lo simplifica.

Con esas dos esquinas del triángulo REST atendidas, posiblemente la mayor área gris esté en identificar y nombrar recursos. Nombrar es un área donde la simplicidad realmente vale la pena, porque es muy fácil equivocarse. Sin embargo, si te apegas a un conjunto estándar de verbos y tipos de contenido religiosamente, te ayudarán a restringir la elección de los sustantivos.

Es muy importante definir URI limpios, legibles y persistentes para sus recursos. Las preguntas frecuentes de REST \(http://rest.blueoxen.net/cgi-bin/wiki.pl?RestFaq\) hacen una buena observación sobre el nombramiento:

GET está restringido a una sola línea URL y ese tipo de aplica un buen principio de diseño que todo lo que es interesante en la Web debe ser direccionable por URL. Si desea crear un sistema donde las cosas interesantes no sean direccionables por URL, entonces debe justificar esa decisión.

Esto es lo que quieren decir los diseñadores y arquitectos cuando dicen que "las restricciones son liberadoras". Los principios de REST se derivaron del examen de cómo funcionan realmente la Web y otras redes de hipertexto. En lugar de ser un conjunto de restricciones arbitrarias, encarnan la forma en que la Web debería actuar.

Al trabajar dentro de los principios de REST, cualquier dolor que pueda sentir se debe tratar como una insinuación de que puede estar yendo en contra de la arquitectura natural de la Web. Ciertamente es posible que su caso particular sea especial. Ciertos dominios de aplicaciones simplemente no encajan bien en el paradigma REST. \(REST ha sido descrito como "Turing-completo" en paralelo con los lenguajes de programación. Aunque cualquier aplicación puede expresarse en términos de REST, algunas pueden ser mucho más conducentes al REST que otras.\) Pero tratando de empujarse a sí mismo hacia el El paradigma REST lo obliga a defender excepciones y casos especiales, y al hacerlo puede descubrir que las excepciones no eran necesarias después de todo.

### Almacenamiento en caché y escalabilidad

REST se adapta perfectamente a la filosofía de nada compartido de Rails: la idea de que la única comunicación entre los servidores de aplicaciones debe ser a través de la base de datos \(en términos de REST, mediante la "modificación del estado de los recursos"\). Anteriormente mencionamos cómo esto ayuda a la escalabilidad; cuantas menos interacciones sean necesarias entre los servidores de aplicaciones, más fácil será escalar horizontalmente cuando la carga lo dicte.

El almacenamiento en caché es bien conocido como un problema difícil. Phil Karlton llamó a la invalidación de la memoria caché como una de las "dos cosas difíciles en informática". Uno de los requisitos de la transferencia de información de TTTP es la misma, ya que la información transmitida debe ser la misma que si no hubiera caché. Lo que esto significa en la práctica depende no solo del tipo de caché que se usa y dónde se ubican dentro de una arquitectura de red, sino también de la semántica de las actualizaciones y de la "frescura" dentro de la propia aplicación.

Como el comportamiento de caché depende tanto de la aplicación, se tomó la decisión de que HTTP proporcionara mecanismos para el almacenamiento en caché sin especificar el comportamiento real o la responsabilidad del cliente / servidor con gran detalle. Como lo describe Fielding \(en la sección 6.3.2.6 de su tesis\):

Como REST intenta equilibrar la necesidad de un comportamiento eficiente y de baja latencia con el deseo de un comportamiento de caché semánticamente transparente, es fundamental que HTTP permita que la aplicación determine los requisitos de caché en lugar de codificarlos en el protocolo mismo. Lo más importante que el protocolo debe hacer es describir de manera completa y precisa los datos que se transfieren, de modo que ninguna aplicación se deje engañar al pensar que tiene una cosa cuando realmente tiene algo más. HTTP / 1.1 hace esto mediante la adición de los campos de encabezado Cache-Control, Age, Etag y Vary.

La sección 13 de la especificación HTTP / 1.1 \(RFC 2616\) detalla el comportamiento de caché de HTTP. \* HTTP proporciona varias características para el control de caché, que interactúan para proporcionar un marco de trabajo en el que servidores, clientes e intermediarios pueden construir una política de almacenamiento en caché. Estas características incluyen lo siguiente:

#### Condicional GET

Un GET condicional es una solicitud HTTP GET que contiene uno o más campos de encabezado adicionales que especifican las condiciones bajo las cuales se debe enviar o no el recurso solicitado. Se usa junto con el encabezado Last-Modified y / o las etiquetas de entidad, que se describen a continuación.

#### Última modificación

El encabezado de respuesta Last-Modified contiene un valor de fecha / hora que indica la hora en que se modificó por última vez el recurso proporcionado. Cuando un cliente tiene una versión \(potencialmente antigua\) de este recurso, puede validar esa versión ejecutando un GET condicional con un encabezado If-Modified-Since establecido en el momento en que se modificó por última vez la versión. Si el servidor tiene una versión más nueva, enviará la nueva versión con un encabezado Last-Modified actualizado. Si no hay una versión más nueva, el servidor enviará una respuesta 304 No modificada sin cuerpo, ahorrando tiempo y ancho de banda.

#### Etiquetas de entidad \(ETags\)

Las etiquetas de entidad son etiquetas cortas que sirven para identificar una versión particular de una entidad. Típicamente, son un hash del cuerpo de respuesta; de esta forma, las entidades se pueden comparar intercambiando la etiqueta en lugar de todo el cuerpo.

Las etiquetas de entidad, como Last-Modified, también usan GET condicional. El cliente envía una solicitud con un encabezado If-No-Match que especifica el ETag de su última versión. Si el servidor calcula un ETag diferente para la versión más nueva de ese recurso, enviará la nueva versión con un encabezado Last-Modified actualizado. Si no hay una versión más nueva, el servidor enviará una respuesta 304 No modificada sin cuerpo, ahorrando tiempo y ancho de banda. todo el cuerpo Pero si la ETag calculada coincide con la que el cliente envió, se devuelve una respuesta 304 No modificada.

Existen muchas estrategias diferentes para calcular las etiquetas de entidad, según las necesidades de la aplicación. La simple estrategia de hash el cuerpo de respuesta funciona, pero puede ser ineficiente ya que el servidor aún debe generar y modificar la respuesta. Para el contenido dinámico, esto puede ser extremadamente ineficiente \(y puede ser más razonable usar If-Modified-Since y compararlo con una marca de tiempo en los datos de origen\).

La solución utilizada a menudo para contenido estático es incorporar el inodo del archivo, ya que puede ser mucho más rápido registrar \(2\) un archivo que abrirlo y leer sus datos en la memoria. Esto funciona bien, pero no se puede usar en clústeres donde los datos residen en sistemas de archivos separados entre los sistemas del clúster.

Rails proporciona compatibilidad ETag transparente, que revisaremos más adelante en este capítulo.

#### Encabezado de caché-control

El encabezado HTTP Cache-Control proporciona una forma de especificar la semántica de caché no cubierta por otros mecanismos HTTTP. Esta cabecera proporciona las siguientes funciones, y más:

• Los clientes pueden solicitar una respuesta que puede ser antigua, pero solo hasta una cierta cantidad de tiempo \(edad máxima\).

• Los clientes y servidores pueden dictar que cierta información nunca sea almacenada en memoria caché intermedia compartida \(privada\), o por cualquier caché \(no-caché\).

• El protocolo de caché puede extenderse sin desglosar la semántica de HTTTP, si así lo acuerdan el cliente y el servidor y se implementa de esta manera.

### Robustez para cambiar

Otra ventaja que confiere el diseño REST es que los diseños tienden a ser más resistentes al cambio que las interfaces de estilo RPC. El diseño RESTful conduce las decisiones arquitectónicas al territorio nominal. La selección de sustantivos tiende a ser impulsada por el dominio, mientras que las interfaces RPC tienden a ser más impulsadas por la implementación, ya que exponen sus procedimientos \(un detalle de implementación\) como parte de la interfaz. Si bien las interfaces RPC a menudo necesitan una capa adicional de direccionamiento indirecto para separar la interfaz de la implementación, REST cultiva una separación de interfaz e implementación alentando interfaces más abstractas.

Además, debido a que REST distingue la idea de "recurso" de "representación", es fácil agregar nuevos tipos de contenido que representen el mismo recurso ya que se requieren nuevos formatos. No se requieren cambios arquitectónicos, ya que REST se basa en una separación entre un recurso abstracto y sus representaciones.

### Uniformidad

Una de las mayores ventajas ofrecidas por las directrices REST es una interfaz uniforme. Los verbos \(en el caso, los métodos HTTTP\) son universalmente uniformes en todos los dominios de aplicación. Los tipos de contenido, aunque no son universales \(difieren entre dominios\), están estandarizados y son relativamente bien conocidos.

El hecho de que esté limitado a un conjunto tan pequeño de métodos puede parecer restrictivo, pero en la práctica no es una preocupación importante. Cualquier cosa que desee modelar se puede enmarcar fácilmente en términos de las operaciones CRUD. Esta forma de pensar acerca del mundo ayuda a impulsar la complejidad esencial en una parte de la arquitectura, donde se puede tratar fácilmente. Por lo general, cuando parece que necesita más que los métodos básicos, hay otra entidad al acecho en el modelo, esperando ser extraída.

Los tipos de contenido están estandarizados de una manera diferente. Los tipos de contenido suelen ser específicos de cada aplicación, por lo que no tendría sentido que fueran universales. Sin embargo, para facilitar la comunicación, los tipos de contenido suelen ser un tanto estándar. EnHTTPworld, esto significa MIME \(MultipurposeInternetMailExtenions\), un estándar de Internet que define un marco para los tipos de contenido. El conjunto de tipos MIME es extensible, por lo que las nuevas aplicaciones pueden definir tipos locales e incluso registrarlos con IANA una vez que se usan ampliamente.

La uniformidad que REST proporciona tremendamente ayuda a los esfuerzos de estandarización. Al unir múltiples sistemas \(como ocurre al desarrollar un estándar\), cuantas menos diferencias haya, menos desacuerdos políticos habrá. Si todos estandarizan en un conjunto de verbos \(que están universalmente estandarizados cuando usan HTTP\), las únicas diferencias que quedan son los tipos de contenido \(que son bastante sencillos de estandarizar dentro de un dominio de aplicación\) y los sustantivos.

Por lo tanto, cumplir con una interfaz RESTful ayuda a reducir el gran problema de la estandarización a los problemas algo más manejables de representar datos \(tipos de contenido\) y nombrar cosas \(sustantivos\). Aunque estas siguen siendo áreas donde el desacuerdo y la política pueden interferir, los principios de REST ayudan a enfocar la discusión al restringir los temas de discusión.



