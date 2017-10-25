# 2.5.1 - Problemas de aplicación

Primero, examinaremos algunos principios importantes que deberían guiar el diseño de cualquier aplicación web.

### Autenticación

La pauta más importante en el área de autenticación es simple:

¡Siempre sal y hash todas las contraseñas!

Hay muy pocas excepciones válidas a esta regla, y aún menos se aplican a aplicaciones web. La única razón posible para almacenar contraseñas en texto sin formato es si deben proporcionarse a un servicio externo en texto sin formato. Incluso entonces, las contraseñas deben cifrarse simétricamente con un secreto compartido, para proporcionar defensa en profundidad.

Examinemos el razonamiento detrás de esta regla. Las contraseñas hash evitan que se recuperen si la base de datos o el código fuente están en peligro. Salarlos los protege de los ataques del arco iris.

Salar es el proceso de garantizar que la misma contraseña se hereda a diferentes valores para diferentes usuarios. Considere el siguiente código, que tiene hashes pero no sal.

### No confíes en el cliente

ActionPack \(ActionController plus ActionView\) facilita mucho las cosas para usted como desarrollador. Sin embargo, elimina todos los detalles del ciclo de solicitud / respuesta de HTTP. Esto generalmente es algo bueno: realmente no desea tratar con todos los detalles del protocolo CGI. Pero es importante no dejar que esta abstracción obstaculice la escritura de código seguro. Uno de los principios fundamentales que debe tener en cuenta es que nunca puede confiar en la información que le envía el navegador web \(el cliente\).

Esta es un área donde la abstracción con fugas que proporciona Rails puede dañar. Realmente vale la pena entender cómo funciona HTTTP, al menos en el punto en que sepas si esa información en particular proviene del cliente, el marco de la aplicación o el entorno. Nunca puede confiar en nada que provenga del cliente, ya que el cliente puede enviar los datos que desee. Puede insertar encabezados falsos, parámetros adicionales, cadenas de consulta mal formadas o lo que quiera. Aquí hay una breve lista de los datos en los que no se puede confiar. Esta no es una lista completa, pero debería hacerte pensar. \*

• Parámetros de formulario \(cadena de consulta y datos POST\): el error más común que se comete es crear los parámetros confiables proporcionados por HTTPrequest. Discutiremos esto más adelante en el capítulo.

• Cookies \(sin embargo, veremos una excepción más adelante\).

• Referer † header, que contiene el URI de la página desde la que se vinculó la página actual. Se incluyó con la intención de ayudar a los webmasters a rastrear los enlaces más importantes. Usarlo con fines de autenticación o seguridad es completamente atrasado.

• Encabezado de agente de usuario, que supuestamente identifica el nombre del software de cliente que está accediendo a la página. Al igual que Referer, esto es principalmente útil para el análisis de registros y nunca debe usarse con fines de seguridad.

Como ejemplo, podemos examinar el diseño de seguridad deficiente desde otra plataforma. PHP tiene una opción de configuración, register\_globals, que puede causar serios problemas de seguridad cuando se establece. Cuando la opción está habilitada, las variables de la cadena de consulta se agregan automáticamente al espacio de nombre global. El ejemplo aburrido pero pedagógico es el del código de autenticación de usuario, que autentica al usuario y luego muestra información secreta según el nivel de acceso del usuario:



