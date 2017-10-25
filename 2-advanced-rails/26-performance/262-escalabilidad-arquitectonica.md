# 2.6.2 - Escalabilidad arquitectónica

Una de las partes más difíciles de construir e implementar una aplicación web es hacerla crecer. Afortunadamente, Rails fue diseñado teniendo en cuenta la escalabilidad. El mantra de escalabilidad de Rails se comparte, nada, la idea de que cada servidor de aplicaciones se soporte por sí mismo, sin tener que coordinarse con otros servidores de aplicaciones para gestionar una solicitud. Lo único que debe compartirse cuando se escala hacia arriba es la base de datos. \*

Sin embargo, hay algunas otras preocupaciones que debe tener en cuenta al escalar una aplicación Rails. Las mayores preocupaciones son el otro estado compartido además de los datos de la aplicación: almacenamiento para sesiones y datos en caché.

### Sesiones

La infraestructura de sesión de Rails se basa en la sesión CGI :: Session de Ruby de la biblioteca estándar. \* CGI :: Session se ocupa de los aspectos básicos de la gestión de sesión CGI. Proporciona las siguientes tiendas de sesión, cada una implementada como una clase dentro de CGI :: Session:

### FileStore

Almacena datos en un archivo como texto sin formato. No se intenta ordenar los datos, por lo que primero debe convertir los datos de la sesión en un String.

### MemoryStore

Almacena datos de sesión de forma nativa en la memoria del proceso de intérprete de Ruby.

### PStore

Similar a FileStore, pero ordena los datos antes de almacenarlos. Esto le permite almacenar cualquier tipo de datos en la sesión. Esta es una buena opción para el desarrollo, pero no es adecuada para un entorno de producción.

Debido a que estas opciones son bastante delgadas y no demasiado adecuadas para aplicaciones web a gran escala, Rails proporciona algunos administradores de sesión que son más útiles. En particular, todas estas opciones permiten que las sesiones se compartan entre diferentes servidores de aplicaciones. Estos implementan la misma interfaz que las otras tiendas CGI :: Session, por lo que son reemplazos directos. Examinaremos cada uno en detalle aquí.

### ActiveRecordStore

Como su nombre lo sugiere, ActiveRecordStore está diseñado para almacenar sesiones en una base de datos a través de ActiveRecord. Sin embargo, es flexible y no requiere estrictamente ActiveRecord. El uso estándar de ActiveRecordStore es muy simple si ya está usando ActiveRecord; simplemente configure el almacén de sesión en config / environment.rb:

     config.action\_controller.session\_store =: active\_record\_store

Se proporciona una tarea de Rake para crear la migración de la base de datos para la tabla de sesiones: rake db: sessions: create. No necesita crear la clase Session de ActiveRecord, ya que ActiveRecordStore lo configura para usted. Esta clase se llama CGI :: Session :: ActiveRecordStore :: Session, por lo que puede hojear sus partes internas si necesita cambiar algo:

     CGI :: Session :: ActiveRecordStore :: Session.table\_name = 'web\_sessions'

ActiveRecordStore utiliza algunas características de ActiveRecord, por lo que realmente funcionará con clases que actúan como ActiveRecord. Esto es útil si de lo contrario no estás usando



