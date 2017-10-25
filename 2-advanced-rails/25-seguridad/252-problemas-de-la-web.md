# 2.5.2 - Problemas de la web

Ahora que hemos examinado algunas de las formas arquitectónicas en las que puede proteger su aplicación, veremos algunos de los problemas endémicos de la Web.

### Sesiones de rails

La mayoría de los marcos web tienen algún tipo de gestión de sesión: un mecanismo persistente de almacenamiento del lado del servidor para los datos específicos de la sesión de navegación de un cliente. El alcance exacto de una "sesión de navegación" depende de los detalles de implementación y del método de seguimiento de la sesión. Más comúnmente, se utiliza una cookie no persistente, por lo que una sesión consiste en todas las visitas a un sitio antes de cerrar el navegador. Alternativamente, se puede usar una cookie persistente \(una con una fecha de caducidad explícita\); esto persistirá incluso cuando el navegador esté cerrado. Esto es útil para recordar información \(como un carrito de compras\) a través de visitas para usuarios anónimos. Algunos frameworks como Seaside ofrecen sesiones basadas en URL \(query-string\) para que un usuario pueda tener múltiples sesiones activas al mismo tiempo en diferentes ventanas del navegador.

La mayoría de los métodos de almacenamiento de sesión de Rails proporcionan las siguientes propiedades:

#### Confidencialidad

Nadie excepto el servidor puede leer los datos almacenados en la sesión.

#### Integridad

Nadie excepto el servidor, incluido el cliente en sí, puede modificar los datos almacenados en la sesión de otra forma que no sea lanzar la sesión y obtener una nueva. Un corolario es que solo el servidor debería poder crear sesiones válidas.

Los métodos de almacenamiento de sesión tradicionales en Rails son del lado del servidor; almacenan todos los datos de la sesión en el servidor, generan una clave aleatoria y la usan como ID de la sesión. El ID de la sesión no está vinculado a los datos que no sea un índice, por lo que es seguro presentarlo al cliente sin comprometer la confidencialidad.

Rails usa tanta aleatoriedad como sea posible para crear la ID de la sesión: se necesita un hash MD5 de la hora actual, un número aleatorio, la ID del proceso y una constante. Esto es importante: las ID de sesión que se pueden adivinar permiten el secuestro de la sesión. En un ataque de secuestro de sesión, un atacante olfatea o adivina una ID de sesión de un usuario autenticado y la presenta al servidor como propia, asumiendo así la sesión del usuario autenticado. Esto es similar a la secuencia de TCP de ataque de predicción, y la mitigación es la misma: haga que los ID de sesión sean lo más aleatorios posible.

### Sesiones basadas en cookies

Hay muchos problemas con el almacenamiento de la sesión del lado del servidor. El almacenamiento basado en archivos, que dirige las sesiones a un archivo local en el servidor, no es escalable: para que funcione en un clúster, necesita un sistema de archivos compartido o un equilibrador de carga que admita sesiones fijas \(dirigiendo todas las solicitudes en el mismo sesión a un servidor en el clúster\). Esto puede ser complicado y a menudo ineficiente. Las sesiones respaldadas por la base de datos resuelven este problema manteniendo el estado de la sesión en una base de datos central. Sin embargo, esta base de datos puede convertirse en un cuello de botella porque debe accederse una vez por cada solicitud que necesite datos de sesión \(como la autenticación de usuario\). El almacén de sesiones de DRb no se usa ampliamente y requiere ejecutar otro proceso de servidor.

Hay una solución a estos problemas. La mayoría de las sesiones de Rails son livianas: por lo general, contienen poco más que una identificación de usuario \(si está autenticada\) y posiblemente un mensaje de flash. Esto significa que pueden almacenarse en el cliente en lugar del servidor. The Rails CookieStore hace justamente esto: en lugar de almacenar la sesión en el servidor y almacenar su ID en el cliente en una cookie, almacena toda la sesión en una cookie.

Por supuesto, la seguridad debe tenerse en cuenta. Recuerde la regla más importante: nunca confíe en el cliente. Si simplemente clasificamos los datos en una cadena y los colocamos en una cookie, no tendríamos forma de evitar que el cliente altere la sesión. El usuario podría simplemente enviar la cookie correspondiente a los datos de sesión user\_id = 1 y engañar al servidor haciéndole creer que estaba conectado como el usuario con ID 1.

Para contrarrestar esto, el CookieStore firma cada cookie con un HMAC \(código de autenticación de mensaje con hash-clave\), que es esencialmente un hash de los datos de cookie junto con una clave secreta. El cliente no puede forjar o modificar sesiones porque no puede generar firmas válidas en los datos modificados. El servidor comprueba el hash en cada solicitud y genera una excepción TamperedWithCookie si el hash no coincide con los datos. Esta es la forma estándar de almacenar datos con un cliente que no es de confianza mientras se asegura la integridad.

En Rails 2.0, el CookieStore ahora es el almacén de sesión predeterminado. El CookieStore requiere una clave o frase secreta y una clave de cookie de sesión para definir; levantará un

