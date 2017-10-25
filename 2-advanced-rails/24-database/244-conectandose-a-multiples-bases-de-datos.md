# 2.4.4 - Conectándose a multiples bases de datos

Ocasionalmente, tendrá la necesidad de conectarse a varias bases de datos diferentes desde una aplicación. Esto es útil para migrar de un esquema antiguo a uno nuevo. También es útil si tiene diferentes requisitos de datos dentro de una aplicación; quizás algunos datos son más críticos y se almacenan en un clúster de base de datos de alta disponibilidad. En cualquier caso, es fácil en Rails. Primero, especifique múltiples entornos de base de datos en el archivo de configuración database.yml:

```ruby
legacy:
      adapter: mysql
      database: my_db
      username: user
      password: pass
      host: legacy_host
new:
      adapter: mysql
      database: my_db
      username: user
      password: pass
      host: new_host
```

Luego, simplemente puede consultar estos bloques de configuración desde la definición de la clase ActiveRecord utilizando el método `ActiveRecord::Base.establish_connection`:

```ruby
 class LegacyClient < ActiveRecord::Base      
  establish_connection "legacy"
 end    
  
  class Client < ActiveRecord::Base      
   establish_connection "new"
  end
```

Este enfoque también funciona con múltiples entornos Rails. Simplemente especifique cada entorno en el archivo database.yml como de costumbre:

```ruby
legacy_development: 
# ...

legacy_test: 
# ...

legacy_production: 
# ...

new_development: 
# ...

new_test: 
# ...

new_production: 
# ...
```

Luego, use la constante RAILS\_ENV en el nombre del bloque de configuración de la base de datos:

```ruby
class LegacyClient < ActiveRecord::Base
      establish_connection "legacy_#{RAILS_ENV}"
end
class Client < ActiveRecord::Base
      establish_connection "new_#{RAILS_ENV}"
end
```



