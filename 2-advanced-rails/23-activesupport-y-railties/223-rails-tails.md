# 2.2.3 - Rails Tails

RailTies es el conjunto de componentes que conectan ActiveRecord, ActionController y ActionView para formar Rails. Examinaremos las dos partes más importantes de RailTies: cómo se inicializa Rails y cómo se procesan las solicitudes.

Configuración de rails

La clase `Rails::Configuration`, definida en `initializer.rb`, contiene los atributos de configuración que controlan Rails. Tiene varios atributos generales de Rails definidos como atributos en la clase de Configuración, pero hay un poco de astucia en los apéndices de la clase de infraestructura. Los cinco stubs de clase \(`action_controller`, `action_mailer`, `action_view`, `active_resource` y `active_record`\) actúan como proxies para los atributos de clase de sus respectivas clases base. De esta manera, la declaración de configuración:

```ruby
config.action_controller.perform_caching = true
```

es lo mismo que

```ruby
ActionController::Base.perform_caching = true
```

excepto con una sintaxis de configuración unificada.



