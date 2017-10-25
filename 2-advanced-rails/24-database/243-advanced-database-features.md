# 2.4.3 - Advanced Database Features

Entre los programadores de Rails, las características avanzadas de la base de datos son a menudo un punto de discusión. Algunos sostienen que las limitaciones, desencadenantes y procedimientos son esenciales; algunos los rechazan por completo, diciendo que la inteligencia pertenece solo a la aplicación. Simpatizo con el argumento de que toda la lógica de negocios pertenece a la aplicación; es casi imposible hacer cambios ágiles a los requisitos cambiantes cuando la lógica se divide entre dos ubicaciones. Aún así, creo que las restricciones, los factores desencadenantes e incluso los procedimientos almacenados tienen su lugar en las aplicaciones empresariales. Para explicar por qué, tendremos que examinar una distinción que aparece a menudo en relación con este debate: la diferencia entre las bases de datos de aplicaciones y de integración.

### Aplicación Versus Bases de datos de integración

Martin Fowler diferencia entre bases de datos de aplicaciones y bases de datos de integración. \* La distinción básica es que una base de datos de integración se comparte entre muchas aplicaciones, mientras que una base de datos de aplicaciones "pertenece" a la aplicación que la usa. En este sentido, "aplicación" puede significar un programa o múltiples programas dentro de un límite de aplicación \(la misma aplicación lógica\). Por lo general, esta distinción se refiere a cómo se organiza el esquema; en Rails, las bases de datos de integración a menudo se denominan bases de datos con "esquemas heredados". En las bases de datos de aplicaciones, la integración puede realizarse a través de mensajes en la capa de aplicación en lugar de la capa de la base de datos.

Rails es obstinado sobre cómo deberían estructurarse sus esquemas de base de datos: la clave primaria debería ser id, las claves externas deberían ser thing\_id y los nombres de tabla deberían ser plurales. Esto no es intolerancia a la base de datos; Rails tiene que elegir un valor predeterminado sensato para que el paradigma "convención sobre configuración" sea efectivo. Es relativamente fácil cambiar casi cualquiera de estos valores predeterminados. Rails funciona bien con las bases de datos de integración.

Muchos desarrolladores de Rails evitan las bases de datos de integración como innecesarias; ellos mantienen que toda la integración debe hacerse en la capa de aplicación. Algunos dan un paso más y afirman que la verificación de la integridad de los datos pertenece solo a la aplicación, para mantener toda la lógica comercial en el mismo lugar. Aunque esto podría ser ideal, el mundo real no siempre es tan agradable. Incluso si se puede realizar toda la integración en el nivel de la aplicación, existen muchas razones válidas para utilizar las restricciones de la base de datos.

Además, la mayoría de las bases de datos en la empresa tienden a convertirse en bases de datos de integración a lo largo del tiempo. Las bases de datos que son útiles para un propósito a menudo se asignan para otro uso. A veces tiene una base de datos que no está bajo su control, y desea utilizar los datos sin realizar un ETL completo \(extraer, transformar, cargar\). Incluso ejecutando un pequeño script en su base de datos sin usar el modelo ActiveRecord, o manteniendo la base de datos manualmente a través de un cliente de consola como mysql o psql, significa que tiene algo accediendo a su base de datos fuera de su modelo de dominio. Si las validaciones en el dominio son la única forma de garantizar que los datos sean consistentes, esto puede ocasionar problemas.

### Restricciones

Las restricciones a nivel de base de datos proporcionan una forma de especificar explícitamente las suposiciones implícitas de una aplicación sobre sus datos. Hay dos tipos de restricciones, que no deben confundirse:

#### Lógica de negocios

"Un gerente no puede administrar más de cinco empleados". La característica clave de las limitaciones de la lógica de negocios es que posiblemente podrían cambiar a lo largo de la vida de la base de datos. Las restricciones lógicas de negocios nunca deberían estar en la base de datos, salvo una muy buena razón para lo contrario.

#### Integridad

"NOS. Los números de seguridad social, cuando se proporcionan, deben contener exactamente nueve dígitos. "Las restricciones de integridad definen la naturaleza de los datos que se representan. Admitidamente, "la naturaleza de los datos" es un concepto algo nebuloso; el significado diferirá entre las bases de datos. Las restricciones de integridad deben residir en la base de datos, sin más motivo que proporcionar una verificación de cordura de último nivel sobre los datos.

Al igual que con cualquier otra área de modelado de datos, hay áreas grises. Un ejemplo sería "el salario de un empleado debe ser positivo", lo que podría concebirse de cualquier manera. \* La ventaja de las restricciones es que reducen el dominio de posibles resultados que la base de datos puede generar. Cuando sabe que el DBMS para una tienda en línea nunca puede arrojar un precio negativo para un producto, puede sumar los precios de las partidas pertenecientes a una orden sin preocuparse por los precios no válidos. Aunque la línea se dibuja en diferentes lugares para diferentes aplicaciones, el principio básico es este: la base de datos no debe hacer cumplir la lógica comercial, pero debe hacer cumplir la coherencia y la integridad.

Independientemente de las diferencias de opinión sobre las restricciones de verificación, un tipo de restricción no es negociable: restricciones de clave externa. Si se requiere una relación de clave externa, un registro no asociado es semánticamente insignificante y no se debe permitir que suceda. Solo tiene sentido práctico formalizar esa asociación.

La única forma verdaderamente sólida de garantizar que una base de datos mantenga la integridad durante años a medida que acumula datos \(como suelen hacerlo las bases de datos\) es declarar restricciones apropiadas sobre los datos. A menos que pueda decir con certeza que cada aplicación o persona que acceda a la base de datos lo hará a través del modelo de dominio \(pasando por todas las validaciones asociadas\), la única opción sensata es tratar la base de datos como una base de datos de integración.

Existe una ventaja adicional al proporcionar restricciones: normalmente, cuantas más restricciones se proporcionen en una base de datos, mejor será el trabajo que el optimizador de consultas puede hacer al crear un plan de consulta.

Una queja común sobre las restricciones de la base de datos es que requieren que especifique información semántica en dos lugares: su base de datos y su código de aplicación \(generalmente quiere atrapar datos inválidos en las validaciones de su aplicación antes de intentar insertarlo en su base de datos, incluso si la base de datos captaría el error de todos modos\). La biblioteca DrySQL † hace un gran esfuerzo para eliminar esta duplicación. Infiere las relaciones de esquema y las reglas de validación a partir de los tipos y restricciones de la base de datos, por lo que no tienen que especificarse en la aplicación. DrySQL funciona con todos los DBMS principales: PostgreSQL 8 y superior, MySQL 5 y superior, SQL Server, Oracle y DB2.

### Llaves compuestas

Las claves compuestas, claves primarias formadas por dos o más atributos, se evitan mejor. No solo son más difíciles de administrar que las claves primarias simples, por lo general son más frágiles. La motivación para usar claves compuestas generalmente se basa en algún aspecto intrínsecamente único de los datos, lo que significa que la clave compuesta será significativa \(vinculada a los datos\) en lugar de insignificante \(vinculada solo a la base de datos\). Por lo general, es mucho más flexible para asignar una clave primaria sin sentido utilizada solo dentro de la base de datos. De esta forma, la integridad de los datos es interna a la base de datos en lugar de estar vinculada a un sistema o proceso externo.

Como ejemplo, considere una base de datos que rastrea a los miembros de los EE. UU. Por sus números de licencia de conducir. Una clave candidata sería {Emisión de estado, número de licencia}. Una ventaja inmediata de una clave sin sentido es que los valores enteros son más fáciles de representar que las listas; es más fácil hacer referencia a un registro como 12345 que como \[IL, 1234\]. Esto simplifica las claves externas y simplifica los servicios web y otros protocolos utilizados para la interoperabilidad.

Pero el problema más básico es que una clave primaria generalmente se trata como un identificador único y estable para un registro. Una clave compuesta puede no ser única en la práctica e incluso puede cambiar. Si tuviera que usar la clave compuesta anterior, debe estar preparado para responder preguntas como las siguientes:

• ¿Qué sucede cuando un miembro se muda o tiene un nuevo número de licencia emitido?

• ¿Qué sucede si cambia alguna característica inherente de la llave? Por ejemplo, ¿cómo lo manejaría si los números de licencia fueran siempre de 9 dígitos y se cambiaran a 10? Este es un problema en general con la eliminación de datos significativos.

• ¿Está preparado para que se rechacen todos los registros con una clave duplicada o faltante? ¿O podría ser deseable que el sistema retenga información inválida por un tiempo hasta que se corrija?

Sin embargo, hay algunas situaciones válidas para usar claves compuestas. Un buen ejemplo es en la replicación multimaster. Un gran problema en la replicación asíncrona de multimaestro es la sincronización de secuencias de teclas primarias. Si inserta dos registros aproximadamente a la misma hora en dos servidores principales, debe haber algún mecanismo para garantizar que los dos servidores emitan valores diferentes para las claves primarias en cada registro, no sea que surjan problemas cuando se replican los registros.

La solución de clave compuesta para el problema de las secuencias multimaestro es emitir a cada servidor una ID y usar eso como parte de la clave; entonces cada servidor puede mantener su propia secuencia independientemente de los demás. Los dos registros podrían tener claves principales de {ServerA, 5} y {ServerB, 5} y no habría conflicto. Tenga en cuenta que este es un uso legítimo de las claves compuestas, ya que las claves no tienen sentido \(en relación con los datos que se almacenan en los atributos\).

Para situaciones como esta, el Dr. Nic Williams ha hecho que las claves compuestas funcionen con ActiveRecord. La gema composite\_primary\_keys está disponible en http: // compositekeys. rubyforge.org/.

Como ejemplo, considere el problema de secuencia de multimaestro discutido anteriormente. Tenemos un modelo de pedido que se replica entre dos servidores mediante la replicación multimaster. Debemos usar una clave compuesta para asegurar claves primarias únicas sin importar en qué servidor se crea una orden. Primero, instalamos la gema:

### Triggers, rules y procedimientos almacenados

Ahora estamos en territorio peligroso. Hagamos saber que probablemente tenga una buena razón para usar triggers, reglas o procedimientos almacenados para cualquier cosa terriblemente complicada. Eso no quiere decir que no tienen ningún propósito; ellos pueden ser salvavidas. Pero deben usarse para abordar un problema o inquietud específica, como la siguiente:

• Un proceso complicado que involucra la búsqueda a través de una gran cantidad de datos \(como OLAP o loganalysis\) puede ser facilitado si se descarga al servidor de bases de datos. Como siempre, la creación de perfiles es clave; La optimización prematura puede costarle la velocidad de ejecución, no solo el tiempo del desarrollador.

• Las preocupaciones que tienen poco que ver con la lógica de la aplicación, como los registros de auditoría, generalmente se pueden mover de forma segura a la base de datos como disparadores.

• PostgreSQL puede usar reglas para crear vistas actualizables. Desafortunadamente, esta es actualmente la única forma de obtener vistas actualizables.

• Cuando utilice objetos grandes de Postgres, debe usar un desencadenador para eliminar el objeto grande cuando se elimine el registro correspondiente \(que contiene el OID del LOB\). Considere esto como una forma de integridad referencial.

• Los tipos extendidos o no nativos usarán procedimientos almacenados para el acceso. PostGIS, una base de datos geoespacial para Postgres, usa funciones para administrar datos e índices espaciales.

• TheTSearch2library, integrado en PostgreSQL8.3andlater, usa funciones para acceder a funciones de indexación de texto completo.

Algunas aplicaciones usan procedimientos almacenados para todo acceso a datos, a fin de hacer cumplir el control de acceso. Esto definitivamente no es el camino de Rails. Aunque se puede hacer que funcione, será más difícil que acceder directamente a tablas y vistas. Las vistas proporcionan un control de acceso suficiente para la mayoría de las aplicaciones empresariales; solo use procedimientos almacenados si es necesario. ActiveRecord puede usar transparentemente las vistas actualizadas como si fueran tablas concretas.



