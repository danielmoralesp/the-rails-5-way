

# 2.4.5 - Caching

Si tiene muchas más lecturas que escrituras, el almacenamiento en caché del modelo puede ayudar a aligerar la carga en el servidor de la base de datos. El caché en memoria estándar en estos días está protegido con memoria. \* Desarrollado para LiveJournal, memcached es un caché distribuido que funciona como una tabla hash gigante. Debido a su simplicidad, es escalable y rápido. Está diseñado para que nunca se bloquee, por lo que no hay riesgo de estancamiento. Hay cuatro operaciones simples en el caché, cada una completando en tiempo constante.

En realidad, puedes usar memcached en varios lugares diferentes en Rails. Está disponible como una tienda de sesiones o un almacén de memoria caché de fragmentos, suponiendo que está instalada la gema ruby-memcache. También se puede usar para almacenar modelos completos, pero recuerde que esto solo será efectivo para aplicaciones en las que se lee mucho fuera de lo normal. Hay dos bibliotecas que cubren el almacenamiento en caché de modelos: cached\_model y acts\_as\_cached.

La biblioteca cached\_model \(http://dev.robotcoop.com/Libraries/cached\_model/index. Html\) proporciona una subclase abstracta de ActiveRecord :: Base, CachedModel. Intenta ser lo más transparente posible, simplemente guardando en caché las consultas simples contra objetos individuales y no tratando de hacer nada elegante. Tiene la desventaja de que todos los modelos en caché deben heredar de CachedModel. El uso de cached\_model es muy simple:

