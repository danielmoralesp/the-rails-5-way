# 3.2 - Hola

Todos los buenos libros de lenguaje de programación comienzan con el obligatorio ejemplo Hello World. Aunque RSpec no es un lenguaje de programación multiuso, a veces se describe como un lenguaje específico de dominio \(DSL\) para describir el comportamiento de los objetos. Del mismo modo, Cucumber es una DSL para describir el comportamiento de las aplicaciones.

Para satisfacer este requisito, crearemos ejemplos de Hello para RSpec y Cucumber. Pero primero lo primero, configuremos el entorno.

### Instalación

Si aún no lo ha hecho, lo primero que tendrá que hacer es instalar las gemas rspec y cucumber.1 Abra un shell y escriba lo siguiente \(es posible que deba agregar un prefijo sudo en algunos sistemas\):

```
gem install rspec --version 2.0.0
```

Ahora escriba rspec --help, y debería ver una salida que comience así:

```
Usage: rspec [options] [files or directories]
```

Si no ve eso, o algo cercano, la instalación falla por varias razones. Si eso sucedió, le recomendamos que envíe un correo electrónico a la lista de correo de rspec-users. Todos los autores de este libro y muchos otros miembros conocedores de la comunidad son miembros y estarán encantados de ayudarlo a resolverlo.2

Asumiendo que todo está bien hasta ahora, lo próximo que debe hacer es instalar cucumber escribiendo esto:

```
gem install cucumber --version 0.9.2
```

Nuevamente, es posible que deba agregar este comando a sudo en algunos sistemas. Ahora escriba cucumber --help, y debería ver una salida que comience algo como esto:

```
Usage: cucumber [options] [ [FILE|DIR|URL][:LINE[:LINE]*] ]+
```

En el caso improbable de una falla en la instalación de Cucumber, consulte al grupo Google de Cucumber para obtener ayuda.3

Ahora que las herramientas están instaladas, es hora de decir ¡hola!

### Hola RSpec

Cree un archivo llamado `greeter_spec.rb` en cualquier lugar de su sistema, ábralo en su editor de texto favorito y escriba el siguiente código:

```ruby
describe "RSpec Greeter" do
  it "should say 'Hello RSpec!' when it receives the greet() message" do
    greeter = RSpecGreeter.new
    greeting = greeter.greet
    greeting.should == "Hello RSpec!"
  end
end
```

Veremos todos los detalles de esto más adelante en el libro, pero brevemente aquí hay una explicación.

Comenzamos declarando un grupo de ejemplo usando el método describe\(\) en la línea 1. En la línea 2, declaramos un ejemplo usando el método it \(\).

Dentro del ejemplo, inicializamos un nuevo RSpecGreeter en la línea 3. Esto es lo dado en este ejemplo: el contexto que configuramos y damos por supuesto como punto de partida.

En la línea 4, asignamos el valor devuelto por el método greet \(\) a una variable de saludo. Este es el cuándo en este ejemplo: la acción en la que estamos enfocados.

Por último, en la línea 5, establecemos la expectativa de que el valor del saludo sea igual a "¡Hola, RSpec!". Este es el siguiente de este ejemplo: el resultado esperado.

_**Como verá a lo largo de este libro, usamos estas tres palabras simples: Dado \(Given\), Cuándo\(When\) y Luego\(Then\), porque los entienden fácilmente los contribuyentes técnicos y no técnicos de un proyecto.**_

Ahora guarde el archivo, abra un shell de comando, cd en el directorio en el que está guardado, y escriba este comando:

```ruby
rspec greeter_spec.rb
```

Debería ver la salida incluyendo esto en el shell:

```
uninitialized constant RSpecGreeter
```

Esto es RSpec que le dice que el ejemplo falló porque todavía no hay una clase RSpecGreeter definida. Para simplificar, simplemente defínnelo en el mismo archivo. Agregando esta definición, el archivo completo debería verse así:

```ruby
class RSpecGreeter
  def greet
    "Hello RSpec!"
  end
end

describe "RSpec Greeter" do
  it "should say 'Hello RSpec!' when it receives the greet() message" do
    greeter = RSpecGreeter.new
    greeting = greeter.greet
    greeting.should == "Hello RSpec!"
  end
end
```

Ejecute nuevamente el archivo escribiendo `rspec greeter_spec.rb` y la salida debería ser algo como esto:

```
.

Finished in 0.00075 seconds1 
example, 0 failures
```

¡Éxito! El punto en la primera línea representa el ejemplo que se ejecutó, y el resumen en la última línea informa que hubo un ejemplo y cero fallas.

Esto es un poco diferente de los ejemplos de Hello World que estamos acostumbrados a ver en los libros de lenguaje de programación porque en realidad no imprime Hello RSpec en la línea de comando. En este caso, los comentarios que recibimos nos dicen que el ejemplo se ejecutó y el código funciona como se esperaba.

### Hola Cucumber

Para Cucumber, vamos a necesitar un poco más de estructura, así que vamos a crear un pequeño directorio de proyectos llamado hello. Dentro del directorio de saludo, agregue dos directorios denominados características y especificaciones, y luego mueva el archivo greeter\_spec.rb del ejemplo RSpec al directorio hello / spec. Ahora crea un archivo `greeter_says_hello.feature` en el directorio de características e ingresa el siguiente texto:

```
Feature: greeter says hello

  In order to start learning RSpec and Cucumber
  As a reader of The RSpec Book
  I want a greeter to say Hello

  Scenario: greeter says hello
  Given a greeter
  When I send it the greet message
  Then I should see "Hello Cucumber!"
```

En el shell, cd a la raíz del proyecto, al directorio de hello y tiper `cucumber features`. Deberías ver un resultado como este:

```
➜  hello git:(master) ✗ cucumber features
Feature: greeter says hello
  In order to start learning RSpec and Cucumber
  As a reader of The RSpec Book
  I want a greeter to say Hello

  Scenario: greeter says hello          # features/greeter_says_hello.feature:7
    Given a greeter                     # features/greeter_says_hello.feature:8
    When I send it the greet message    # features/greeter_says_hello.feature:9
    Then I should see "Hello Cucumber!" # features/greeter_says_hello.feature:10

1 scenario (1 undefined)
3 steps (3 undefined)
0m0.869s

You can implement step definitions for undefined steps with these snippets:

Given("a greeter") do
  pending # Write code here that turns the phrase above into concrete actions
end

When("I send it the greet message") do
  pending # Write code here that turns the phrase above into concrete actions
end

Then("I should see {string}") do |string|
  pending # Write code here that turns the phrase above into concrete actions
end
```

Analizaremos más adelante los detalles de esta salida, pero lo mejor es que vemos el texto de la característica y el escenario del archivo `greeter_says_hello.feature`, un resumen de todo lo que se ejecutó y, a continuación, algunos fragmentos de código que podemos usar para nuestras definiciones de pasos.

Una definición de paso es un método que crea un paso. En este ejemplo, usamos los métodos `Given()`, `When()` y `Then()` para escribir definiciones de pasos, cada una de las cuales toma una Regexp y un bloque. Cucumber leerá el primer paso en el escenario, Dando un saludo; busque una definición de paso cuya expresión regular coincida con ese paso; y luego ejecuta el bloque de esa definición de paso.

Para que este escenario pase, debemos almacenar definiciones de pasos en un archivo que Cucumber puede cargar. Continúe y agregue un directorio step\_definitions dentro de hello / características, y agregue un archivo llamado greeter\_steps.rb con el siguiente código:

