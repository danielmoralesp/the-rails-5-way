# 2.4.2 - Large/Binary Objects

Tarde o temprano, muchas aplicaciones web deben lidiar con el problema de los datos LOB \(objetos grandes\). Los datos LOB pueden ser pequeños, pero generalmente son grandes en comparación con otros atributos almacenados \(decenas de kilobytes a cientos de gigabytes o más\). La característica definitoria de los datos LOB, sin embargo, es que la aplicación no tiene conocimiento de la semántica de la estructura interna de los datos.

El ejemplo canónico es datos de imágenes; una aplicación web generalmente no necesita conocer los datos en un archivo JPEG que representa el avatar de un usuario, siempre que pueda enviarlo al cliente, reemplazarlo y eliminarlo cuando sea necesario.

El almacenamiento LOB generalmente se divide en CLOB \(objeto grande de caracteres\) para datos de texto y BLOB \(objeto grande binario\) para todo lo demás. Algunos DBMS separan los dos como tipos de datos separados. Los tipos CLOB a menudo se pueden indexar, cotejar y buscar; BLOB no puede.

### Almacenamiento de la base

Los tipos de DBA entre nosotros pueden preferir el almacenamiento de la base de datos de objetos grandes. Desde un punto de vista teórico, el almacenamiento de datos binarios en la base de datos es la solución más limpia y directa. Ofrece algunas ventajas inmediatas:

• Todos los datos de su aplicación se encuentran en el mismo lugar: la base de datos. Solo hay una interfaz para los datos, y un programa es responsable de administrar los datos en todas sus formas.

• Tiene una mayor flexibilidad con el control de acceso, lo que realmente ayuda cuando se trabaja con proyectos a gran escala. Permiso de DBMS, se pueden asignar diferentes permisos a diferentes tablas dentro de la misma base de datos.

• Los datos binarios no están vinculados a una ruta de archivo físico; Al utilizar el almacenamiento del sistema de archivos, debe actualizar las rutas de archivos en la base de datos de referencia si mueve la ubicación de almacenamiento.

Sin embargo, hay muchas consideraciones prácticas, dependiendo de la implementación de objetos grandes por parte de su DBMS.

#### PostgreSQL

PostgreSQL tiene un soporte francamente extraño para datos binarios. Hay dos formas de almacenar datos binarios en una base de datos PostgreSQL: el tipo de datos BYTEA y objetos grandes.

El tipo BYTEA \* es lo más parecido que PostgreSQL tiene a un tipo BLOB, solo una secuencia de bytes, pero es realmente terrible para grandes cantidades de datos binarios. El protocolo para el envío de tipos BYTEA desde la base de datos requiere escaparse de todos los bytes no imprimibles, por lo que un único byte nulo se codificaría como la cadena ASCII \ 000 \(4 bytes\). No hace falta decir que esto provoca una expansión innecesaria de los datos. Además, es imposible transmitir datos desde la base de datos al navegador web sin ejecutarlo a través de un filtro de exclusión. Sacar un archivo binario de 2 MB de la base de datos generalmente significa transmitir alrededor de 6 MB de datos a través del código de escape. † El método ingenuo ejecuta todos los datos a través de cadenas de Ruby, donde se agiganta enormemente en la memoria. Una mejor opción sería hacer que la biblioteca de postgres C maneje las citas y los comicios, pero esto es mucho trabajo y aún no es óptimo. Se pueden almacenar hasta 1 GB de datos en una columna BYTEA.

La otra opción son objetos grandes. Las características de objetos grandes en PostgreSQL funcionan bastante bien, pero también son un poco torpes. Los archivos se guardan en el catálogo del sistema pg\_largeobject en páginas pequeñas. ‡ Se mantiene un puntero en la tabla de referencia del OID \(Id. De objeto\) del archivo. Se pueden almacenar hasta 2 GB de datos en un objeto grande. Este método es rápido y tiene buenas API, pero hay inconvenientes. No hay control de acceso por tabla o por objeto; el catálogo pg\_largeobject es global para la base de datos, y cualquier persona con permiso para conectarse a la base de datos puede acceder a él. El mecanismo de objeto grande también se descarta ligeramente a favor del almacenamiento en tabla, ya que la técnica de almacenamiento TOAST permite almacenar valores de hasta 1 GB de longitud directamente como atributos dentro de la tabla.

Mi recomendación es usar el almacenamiento del sistema de archivos para todos los objetos binarios si usa PostgreSQL. Aunque la base de datos puede ser el lugar más adecuado para este tipo de datos, simplemente no funciona lo suficiente todavía. Si tiene que usar la base de datos, los objetos grandes realmente funcionan bastante bien. Evite BYTEA a toda costa.

### Almacenamiento de FileSystrem

La realidad es que el almacenamiento del sistema de archivos es la mejor opción, como regla general. Los sistemas de archivos están optimizados para manejar grandes cantidades de datos binarios y / o de caracteres, y son rápidos en eso. El kernel de Linux tiene syscalls como sendfile \(\) que funcionan en archivos físicos. Hay cientos de utilidades de terceros que solo puede aprovechar cuando usa archivos físicos:

• El procesamiento de imágenes es posiblemente la aplicación más popular para almacenar datos binarios. Programas como ImageMagick son mucho más fáciles de usar en su forma de línea de comandos, operando en archivos, en lugar de tener bibliotecas problemáticas como RMagick para trabajar con Ruby.

• Los archivos físicos pueden compartirse con NFS o AFS, colocarse en un host MogileFS o agruparse de otra manera. Lograr una alta disponibilidad o equilibrio de carga con objetos grandes de la base de datos puede ser complicado.

• Cualquier otra utilidad que los archivos de trabajo deberán integrarse o modificarse de otra manera para trabajar desde una base de datos.

#### ¿Por qué el almacenamiento del sistema de archivos es tan rápido?

La respuesta corta es que los servidores web están optimizados para arrojar archivos binarios a través de un TCPsocket. Y la mayoría de las cosas que se guardan con los archivos binary te permiten verlos en un socket TCP.

Respuesta larga: el secreto de este rendimiento, bajo Linux y varios BSD, es el kernel sendfile \(\) syscall \(que no debe confundirse con X-Sendfile, que se tratará más adelante\). La función sendfile \(\) copia datos rápidamente desde un descriptor de archivo \(que representa un archivo abierto\) a un socket \(que está conectado al cliente\). Esto sucede en modo kernel, no en modo usuario: todo el proceso lo maneja el sistema operativo. El servidor web ni siquiera tiene que pensar en eso. Cuando se invoca sendfile \(\), el proceso se parece un poco a la Figura 4-1.

Por otro lado, Rails está necesariamente involucrado con todo el proceso cuando lee datos de la base de datos. El archivo se debe pasar, en bloques, desde la base de datos a Rails, que crea una respuesta y envía todo \(incluido el archivo\) al servidor web. El servidor web luego envía la respuesta al cliente. Usar sendfile \(\) sería imposible aquí porque los datos no existen como un archivo. Los datos deben estar almacenados en la memoria y toda la operación se ejecuta en modo de usuario. El archivo completo se procesa varias veces por código de modo de usuario, que es un proceso mucho más complicado, como se muestra en la Figura 4-2.

### Sirviendo archivos estáticos

Una ventaja del almacenamiento del sistema de archivos es que, siempre que los datos del archivo no necesiten protegerse con el control de acceso o que se actúe de forma dinámica, puede aprovechar sus servidores web estáticos para publicar esos datos. Al exportar la ruta de almacenamiento a través de NFS \(o un sistema de almacenamiento en caché como AFS para conservar el ancho de banda\), puede compartir los archivos de la aplicación con los servidores estáticos en su red de distribución de contenido. Esto elimina por completo la carga de los servidores de aplicaciones y proporciona una solución más escalable.

### Administrar cargas en rails

La mayoría de las aplicaciones que usan objetos grandes deben lidiar con las cargas. Esto puede ser complicado en cualquier marco, pero Rails maneja la mayoría de los detalles y hay algunas mejores prácticas para guiarlo con el resto.

#### Complementos de archivos adjuntos

Una de las maneras más fáciles de manejar las cargas de Rails es usar uno de los populares complementos para el proceso de carga. El plugin estándar solía ser acts\_as\_attachment \(http://svn.techno-weenie.net/projects/plugins/acts\_as\_attachment/\) de Rick Olson. Muchos desarrolladores de Rails están familiarizados con su interfaz, y durante bastante tiempo fue la forma estándar de manejar los datos cargados. Sin embargo, hubo algunos factores que lo hicieron inadecuado para muchas aplicaciones:

• Está vinculado a RMagick \(y por lo tanto ImageMagick\) para el procesamiento de imágenes. ImageMagick es notoriamente difícil de instalar, principalmente porque depende de muchas bibliotecas back-end para procesar diferentes formatos de imagen. En el momento en que se escribió acts\_as\_attachment, ImageMagick fue la mejor opción. Ahora, sin embargo, hay una alternativa más ligera, ImageScience, basada en la biblioteca FreeImage.

• Todos los datos adjuntos deben leerse en la memoria y convertirse en una cadena de Ruby. Para archivos grandes, esto es costoso: Rails le pasa a la aplicación un TempFile, que está sorbido en una cadena. ¡Si utiliza el almacenamiento del sistema de archivos, la secuencia se vuelve a escribir en un archivo!

• No hay soporte para métodos de almacenamiento alternativos como el S3 de Amazon.

Afortunadamente, hay una alternativa. Rick ha reescrito acts\_as\_attachment para resolver estos problemas. La reescritura se llama attachment\_fu, y está disponible públicamente en http: // svn.techno-weenie.net/projects/plugins/attachment\_fu/.

La biblioteca attachment\_fu admite todas las opciones de acts\_as\_attachment y más. Puede usar RMagick como procesador, pero también es compatible con MiniMagick \(una alternativa ligera a RMagick que todavía incluye ImageMagick\) e ImageScience. Puede almacenar archivos adjuntos en una base de datos, el sistema de archivos o S3 de fábrica. También tiene excelentes instalaciones para la expansión; es fácil escribir su propio procesador o back-end de almacenamiento. Un uso típico de attachment\_fu se ve así:

