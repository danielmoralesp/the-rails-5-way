# 2.4.1 - Sistemas de gestión de bases de datos

La comunidad de Rails se ha construido en torno al sistema de gestión de la base de datos MySQL \(DBMS \*\) durante años. Sin embargo, todavía hay muchos conceptos erróneos sobre los SGBD, especialmente cuando se usan con Rails. Si bien MySQL tiene su lugar, ciertamente no es la única opción. En los últimos años, el soporte para otras bases de datos ha crecido enormemente. Lo aliento a mantener la mente abierta a lo largo de este capítulo y sopesar todos los criterios antes de tomar una decisión sobre un SGBD.

Rails admite muchos DBMS; En el momento de escribir estas líneas, se admiten DB2, Firebird, FrontBase, MySQL, OpenBase, Oracle, PostgreSQL, SQLite, Microsoft SQL Server y Sybase. Probablemente sabrá si necesita usar un DBMS diferente a los mencionados aquí. Compruebe el RDoc para el adaptador de conexión para cualquier advertencia específica de su DBMS; algunas funciones, como las migraciones, solo se admiten en un puñado de adaptadores de conexión.

### PostgreSQL

Enumero PostgreSQL \* primero porque es mi plataforma de elección. Es una de las bases de datos de código abierto más avanzadas disponibles en la actualidad. Tiene una larga historia, que se remonta a la Universidad de California en el proyecto Ingres de Berkeley desde principios de los años ochenta. En contraste con MySQL, Postgres ha soportado funciones avanzadas tales como desencadenantes, procedimientos almacenados, tipos de datos personalizados y transacciones durante mucho más tiempo.

El soporte de PostgreSQL para la concurrencia es más maduro que el de MySQL. Postgres admite el control de concurrencia multiversión \(MVCC\), que es incluso más avanzado que el bloqueo a nivel de fila. MVCC puede aislar transacciones, usando marcas de tiempo para dar a cada transacción simultánea su propia instantánea del conjunto de datos. Bajo el nivel de aislamiento Serializable, esto evita problemas tales como lecturas sucias, lecturas no repetibles y lecturas fantasmales. † Consulte la próxima barra lateral, "Control de concurrencia de multiversión", para obtener más información acerca de MVCC.

Una ventaja que PostgreSQL puede tener en la empresa es su similitud con bases de datos empresariales comerciales como Oracle, MS SQL Server o DB2. Aunque Postres no es de ninguna manera una clonación o emulación de ninguna base de datos comercial, será, sin embargo, familiar para los programadores y DBA que tengan experiencia con una de las bases de datos comerciales. También será más fácil migrar una aplicación de Postgres a \(digamos\) Oracle que de MySQL a Oracle.

PostgreSQL tiene una desafortunada reputación de ser lento. Obtuvo esta reputación porque la configuración predeterminada está optimizada para el rendimiento en una máquina pequeña. Por lo tanto, funcionará de manera bastante uniforme en un servidor con tan solo 64 MB de RAM o hasta 64 GB. Al igual que cualquier base de datos, Postgres debe ser ajustado para cualquier uso serio. La documentación oficial en http://www.postgresql.org/docs/ tiene mucha información excelente sobre la optimización del rendimiento.

Una desventaja del uso de PostgreSQL es que tiene una comunidad más pequeña a su alrededor. Hay más desarrolladores, especialmente en el mundo de Rails, que trabajan con MySQL. Hay más soluciones probadas construidas alrededor de MySQL que PostgreSQL. La compañía detrás de MySQL, MySQL AB, proporciona soporte comercial para su producto. No existe una estructura de soporte centralizada para Postgres, ya que no existe una sola compañía detrás de PostgreSQL; Sin embargo, hay varias compañías que se especializan en consultoría de Postgres y brindan contratos de soporte.

### Control de concurrencia multiversion

El control de concurrencia multiversión \(MVCC\) es una de las formas más potentes para lograr el aislamiento entre transacciones concurrentes de bases de datos. MVCC proporciona a cada transacción una instantánea de los datos a los que accede, ya que los datos existían al inicio de la transacción. La transacción realiza acciones en los datos, que se registran con marcas de tiempo. Cuando la transacción se compromete, el DBMS verifica los registros para asegurarse de que no haya conflictos con otras transacciones; si la transacción se puede realizar con éxito, se aplica a la base de datos de forma atómica.

La alternativa a MVCC es el bloqueo a nivel de fila, que es utilizado por el motor de almacenamiento InnoDB de MySQL. El bloqueo a nivel de fila bloquea solo las filas afectadas por una actualización durante una transacción \(a diferencia del bloqueo a nivel de página o tabla, que son más generales\). La ventaja principal que MVCC tiene sobre el bloqueo es que MVCC no bloquea los lectores. Dado que todas las transacciones de actualización se aplican atómicamente, la base de datos siempre está en un estado constante. Las transacciones pendientes se almacenan como registros junto con la base de datos para escribirse en la confirmación, en lugar de aplicarse a la base de datos en el medio de la transacción. La consecuencia más importante de esto es que las lecturas nunca se bloquean, ya que se leen de la base de datos, que siempre es coherente.

Es importante darse cuenta de que el aislamiento para las transacciones simultáneas generalmente se compara con el rendimiento. MVCC usa más espacio de almacenamiento que bloqueo porque tiene que almacenar una instantánea para cada transacción en progreso. Y aunque MVCC nunca bloquea las lecturas, el DBMS puede revertir las transacciones de actualización si causan un conflicto.

### MySQL

El DBMS MySQL es controvertido. Algunos sostienen que es un juguete, mientras que otros consideran que es una buena base para las aplicaciones web. Sin embargo, MySQL es el DBMS dominante en uso para las aplicaciones web de Rails en la actualidad, y ha mejorado mucho entre las versiones 3 y 5.

Parte del mantra de escalabilidad de Rails es "nada compartido": cada servidor de aplicaciones debe ser capaz de sostenerse por sí mismo. Por lo tanto, puede arrojar cinco de ellos detrás de un equilibrador de carga y no importa si un servidor es servido por diferentes servidores durante el transcurso de una sesión. Sin embargo, el cuello de botella es la base de datos. Una gran suposición de esta arquitectura compartida es que los servidores de aplicaciones comparten una base de datos. Si usa una base de datos que no tiene una gran compatibilidad con la concurrencia, tendrá problemas.

Las versiones anteriores de MySQL tenían algunos problemas bastante serios, muchos de ellos relacionados con la integridad y las limitaciones de los datos. El problema no era tanto que los problemas existieran ya que los desarrolladores de MySQL parecían tener una actitud de "no lo van a necesitar". Incluso las transacciones no son compatibles con el motor de almacenamiento predeterminado \(MyISAM\) hasta el día de hoy. En versiones anteriores a 5.0, había muchos errores que descartaban datos incorrectos en lugar de generar un error. Para ser justos, las nuevas versiones de MySQL están abordando muchos de sus problemas. Todavía recomendaría PostgreSQL como regla general, donde la velocidad no es el criterio principal, ya que ha tenido características de nivel empresarial por mucho más tiempo. Si usa MySQL, tome estas recomendaciones:

• Use la versión 5.0 o posterior. Muchos de los problemas que existían con versiones anteriores se han solucionado o mejorado en 5.0 y versiones más recientes.

• Use InnoDB para cualquier cosa donde la integridad de datos o la concurrencia sean importantes. MyISAM, el motor predeterminado en la mayoría de las instalaciones de MySQL, no admite características que la mayoría de los RDBMS consideran esenciales: restricciones de clave externa, bloqueo a nivel de fila y transacciones. En la mayoría de los entornos de negocios, estas características no son negociables. InnoDB es un motor de almacenamiento basado en diario que es mucho más resistente a las fallas. Rails hace lo correcto aquí y se predetermina al motor de almacenamiento InnoDB cuando crea tablas.

Desafortunadamente, InnoDB puede ser mucho más lento que MyISAM, y los tamaños de las tablas suelen ser varias veces más grandes. MyISAM suele ser más rápido cuando lee gran cantidad de sobrescritos o viceversa, mientras que InnoDB generalmente es más rápido cuando las lecturas y escrituras están equilibradas. Todo se reduce a los requisitos de la aplicación específica; estas son reglas generales. Siempre debe comparar con sus datos reales y una muestra precisa de consultas y declaraciones que emitirá, en un entorno realista.

Hay algunas excepciones a esta directriz: MyISAM puede ser una mejor opción si necesita indexación de texto completo \(que solo se admite en las tablas MyISAM en este momento\). Además, si la velocidad bruta de las lecturas o escrituras es la principal preocupación, MyISAM puede ayudar. Por ejemplo, un servidor de registro para análisis web puede usar tablas MyISAM: desea poder volcar registros lo más rápido posible, y las lecturas se realizan con mucha menos frecuencia que las escrituras.

• Establezca el modo SQL en TRADICIONAL. Esto se puede lograr con el siguiente comando:

         SET GLOBAL sql\_mode = 'TRADITIONAL';

Esto hará que MySQL sea un poco más estricto, generando errores en los datos incorrectos en lugar de descartarlos silenciosamente.

MySQL tiene algunas ventajas claras sobre PostgreSQL en algunas situaciones. En general, MySQL tiende a ser más rápido. Para muchas aplicaciones web, la velocidad de consultas puede ser el factor más importante. MySQL también tiene opciones de clonación y replicación más estables y probadas disponibles. MySQL también es un tanto mejor en el manejo de datos binarios almacenados en la base de datos \(lo discutiremos más adelante en este capítulo\). Para muchas aplicaciones web, MySQL puede ser una clara victoria.

### SQLite

SQLite es una base de datos minimalista que es excelente para proyectos pequeños. Aunque no admite muchas características sofisticadas, es una gran opción para proyectos que no crecerán demasiado. Admite transacciones ACID \* de fábrica. SQLite es una biblioteca que está vinculada a su programa; no hay un proceso de servidor para hablar. El código de la biblioteca que reside en el espacio de proceso de su aplicación accede a un archivo de base de datos.

SQLite no proporciona concurrencia, ya que no existe un proceso de servidor para aplicar las propiedades ACID. Por lo tanto, utiliza el bloqueo a nivel de archivo: todo el archivo de la base de datos está bloqueado en el nivel del sistema de archivos durante una transacción. Aún así, para muchas aplicaciones pequeñas, encaja perfectamente. Es un buen reemplazo para los datos que pueden haber sido almacenados en archivos planos, ya que es compatible con la mayor parte del estándar SQL-92 y sería fácil migrar a un DBMS más tradicional a medida que crecen las necesidades.

### Microsoft SQL Server

Aunque Rails creció en el mundo de Linux / Unix, también ha desarrollado una gran compatibilidad con la comunidad para la plataforma de Windows. En Rails no solo se admiten las conexiones de base de datos de Microsoft SQL Server, también existen disposiciones para conectarse a SQL Server desde un sistema basado en Linux, utilizando la biblioteca FreeTDS. †

Desde un cliente de Windows, el enfoque estándar es usar Ruby-DBI \(un adaptador independiente de la base de datos Ruby\) con ADO. La configuración se ve así:

      desarrollo:

        adaptador: sqlserver

        host: server\_name

        base de datos: my\_db

        nombre de usuario: usuario

        contraseña: pase

Su configuración puede variar, dependiendo de la versión de SQL Server y las bibliotecas ADO que haya instalado. Una vez que la configuración de la base de datos está en su lugar, los métodos estándar de ActiveRecord API se pueden usar para manipular datos.

