# 1- Configuración y entornos de Rails

\[Rails\] ganó mucho de su enfoque y atractivo porque no traté de agradar a las personas que no compartían mis problemas. La diferenciación entre producción y desarrollo fue un problema muy real para mí, así que lo resolví de la mejor manera que sabía.

-David Heinemeier Hansson

Las aplicaciones de rails siempre han sido preconfiguradas con tres modos de operación estándar: desarrollo, prueba y producción. Estos modos son básicamente entornos de ejecución y tienen una colección de configuraciones asociadas que determinan cosas tales como a qué base de datos conectarse y si las clases de su aplicación deben recargarse con cada solicitud. Es simple crear sus propios entornos personalizados si es necesario.

El entorno actual se puede especificar a través de la variable de entorno `RAILS_ENV`, que nombra el modo de operación deseado y corresponde a un archivo de definición de entorno en la carpeta `config/environments`. También puede configurar la variable de entorno `RACK_ENV` o, como último recurso, puede confiar en que el desarrollo sea predeterminado. Dado que esta configuración de entorno rige algunos de los aspectos más fundamentales de rails, como la carga de clases, para comprender realmente la forma de rails, debe comprender su configuración de entorno.

El aspecto más fundamental de la configuración de la aplicación es su lista de bibliotecas externas de las que depende. Por lo tanto, iniciamos este capítulo al cubrir Bundler, una herramienta que administra las dependencias de Rubygem para las aplicaciones de Ruby. Toma un archivo de manifiesto como entrada y puede buscar, descargar e instalar las gemas en el manifiesto, además de todas y cada una de las dependencias secundarias. Un poco más adelante en el capítulo pasamos a cómo Rails se inicia y maneja las solicitudes, mediante el examen de scripts como `boot.rb` y `application.rb` y las configuraciones que conforman las tres configuraciones de entorno estándar \(modos\). También cubrimos algunos de los conceptos básicos para definir sus propios entornos y por qué puede elegir hacerlo.

Tenga en cuenta que este libro no está escrito con los recién llegados absolutos a Rails en mente. Para sacar el máximo provecho de este libro, ya debería estar al menos un poco familiarizado con la manera de arrancar una aplicación Rails y el patrón arquitectónico MVC \(Model-View-Controller\). Si no lo eres, te recomiendo que primero aproveches el excelente sitio web del Tutorial de Ruby on Rails2 por Michael Hartl, un autor de la serie Addison-Wesley Professional Ruby.

> Modo API
>
> En lugar de utilizar Rails para generar HTML en el lado del servidor, una tendencia en curso en el desarrollo web es escribir aplicaciones de cliente enriquecido que se ejecutan en el navegador utilizando tecnologías como Angular￼ o react. Estas aplicaciones cliente tratan sus backends de Rails como solo un servidor API, y generalmente se comunican a través de JSON.
>
> Rails 5 introduce un "Modo API" para iniciar nuevas aplicaciones que no se utilizarán para servir aplicaciones de navegador tradicionales basadas en HTML. Cubrimos los detalles del Modo API en el Capítulo 23, "Modo API".

### Bundler

Bundler no es una tecnología específica de Rails, pero es la forma preferida de administrar las dependencias de Rubygem de su aplicación. Las aplicaciones generadas con Rails usan Bundler automáticamente, y no debería necesitar instalar la gema bundler por separado ya que es una dependencia de Rails.

Una de las cosas más importantes que hace Bundler es la resolución de dependencia en la lista completa de gemas especificadas en su configuración, todas a la vez. Esto difiere del enfoque de resolución de dependencia uno a uno empleado por Rubygems y versiones anteriores de Rails, que puede \(y a menudo lo hizo\) dar como resultado el siguiente problema difícil de arreglar.

Supongamos que su sistema tiene instaladas las siguientes versiones de Rubygem:

```
activesupport 4.0.2
activesupport 3.2.11
activemerchant 1.29.3
rails 3.2.11
```

Resulta que activemerchant 1.29.3 depende de activesupport&gt; = 2.3.14. Por lo tanto, cuando lo cargas usando el comando gem \(de la biblioteca RubyGems\) como este

The Rails 5 Way

```
gem 'activemerchant', '1.29.3'
```

### Gem Locking

Cada vez que ejecutas bundle install o bundle update, Bundler calcula el árbol de dependencias para tu aplicación y almacena los resultados en un archivo llamado Gemfile.lock que se parece un poco a esto:

```
GEM
  remote: https://rubygems.org/
  specs:
    actioncable (5.0.0.1)
      actionpack (= 5.0.0.1)
      nio4r (~> 1.2)
      websocket-driver (~> 0.6.1)
    actionmailer (5.0.0.1)
      actionpack (= 5.0.0.1)
      actionview (= 5.0.0.1)
      activejob (= 5.0.0.1)
      mail (~> 2.5, >= 2.5.4)
      rails-dom-testing (~> 2.0)
```

Una vez que se crea un archivo de bloqueo, Bundler solo cargará las versiones específicas de las gemas que estaba utilizando en el momento en que se bloqueó el Gemfile, con la idea de que bloquee su configuración para usar versiones de dependencias que sepa que funcionarán bien con su solicitud.

> Nota
>
> El archivo Gemfile.lock siempre se debe verificar en el control de la versión, para garantizar que cada máquina que ejecuta la aplicación utilice las mismas versiones exactas de gems.7
>
> Para ilustrar la importancia de esto, imagina que Gemfile.lock falta y la aplicación se está implementando en producción. Como el árbol de dependencias no existe, Bundler tiene que resolver todas las gemas del Gemfile en esa máquina. Esto en consecuencia puede instalar versiones de gemas más nuevas que las probadas, causando problemas imprevistos.

### Packaging gems

Puede empaquetar todas sus gemas en el directorio de `vendor/cache` dentro de su aplicación Rails:

```
$ bundle package
```

Ejecutar `bundle install --local` en una aplicación con gemas empaquetadas usará las gemas en el paquete y omitirá la conexión a rubygems.org o cualquier otra fuente de gemas. Puede usar esto para evitar dependencias externas en el momento del despliegue o si depende de gemas privadas que no están disponibles en ningún repositorio público.

> Haciendo que las dependencias de gem estén disponibles para scripts que no sean de Rails
>
> Los scripts que no son Rails se deben ejecutar con bundle exec para obtener un entorno RubyGems correctamente inicializado:
>
> `$ bundle exec guard`

### Bin Stubs

Al iniciar una nueva aplicación, se crearán binstubs para los ejecutables de Rails, ubicados en la carpeta bin. Un binstub es un script que contiene un ejecutable que se ejecuta en el contexto del paquete. Esto significa que no es necesario prefijar el paquete exec cada vez que se invoca un ejecutable específico de Rails. Los binstubs son ciudadanos de primera clase de su proyecto y deben agregarse a su sistema de control de versiones como cualquier otro archivo de código fuente.

Por defecto, los siguientes stubs están disponibles en cada nuevo proyecto de Rails:

```
• bin/bundle • bin/rails • bin/rake
• bin/setup • bin/spring • bin/update
```

Para agregar un binstub de un ejecutable de uso común en su paquete, invoque los binstubs del paquete a algún nombre de gema. Para ilustrar, considere el siguiente ejemplo,

```
$ bundle binstubs guard
```



