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



