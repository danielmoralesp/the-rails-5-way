# Inicio

¿Por qué estás leyendo este libro? Rails es ya viejo y pasado de moda. Todo el mundo sabe que Node.js es el nuevo escenario.

Es una broma. Es cierto que los programadores de Ruby y Rails por igual podrían aprender mucho de Node. Pero Rails es una leña lenta, un viejo calor que de alguna manera no ha desaparecido todavía.

Realmente, la pregunta no es "¿por qué estás leyendo este libro?" Lo más horrible de este libro es que probablemente no lo leas. La pregunta, en realidad, es "¿por qué no la estás leyendo?" Lo que escuché es que se supone que este libro es una referencia para los programadores de Rails intermedios a expertos. Esta no es la forma en que lo aborde en absoluto.

Empecé en Rails en diciembre de 2005, cuando era completamente nuevo, pero en 2010 más o menos, había olvidado mucho de lo que sabía, y Rails había cambiado mucho. Así que leí una edición anterior de este libro de principio a fin, y estaba un poco avergonzado, porque pensé que era un libro para que los novatos lo leyeran así. Pensé que cada novato de Rails estaba haciendo lo que estaba haciendo.

De alguna manera, olvidé que en la industria tecnológica, aprender cómo funciona algo antes de usarlo es exótico y mágico. En la exigente y exigente escuela de Rails que dirigiría si no tuviera nada mejor que hacer con mi vida, se te requerirá que leas este libro de principio a fin, junto con Rails Anti-Patterns, otro libro de esta serie, antes alguna vez se te permitió escribir una línea de código. Incluso tendrías que memorizar partes de eso. Por supuesto, nadie hace eso antes de construir aplicaciones de Rails, y pocas personas lo hacen.

Desafortunadamente, esto significa que cuanto más aprenda sobre Rails, más verá que las personas lo usan incorrectamente. Pero lo bueno es que leer este libro es una muy buena idea. Es extraño que deba decir esto, pero al comprar este libro es un buen primer paso, aún no has terminado. Leerlo también es una muy buena idea.

No soy solo yo. Si lees el prólogo de Steve Klabnik para la edición anterior de este libro, verás que también leyó una edición de este libro de principio a fin, y que esto fue suficiente para catapultarlo de novato de Rails a superhéroe hacker superstar.

No pretendo disminuir su trabajo, por supuesto; también ha trabajado mucho en código abierto en Rails, mucho más que yo, y desde entonces ha desarrollado y compartido su experiencia en REST y Rust. \(Presumiblemente, cuando aparezca una tecnología llamada Rist, Rast o Rost, también será un experto en eso. Es una especialidad extraña, pero claramente la tiene bloqueada\). Pero sí quiero sugerir una posibilidad controvertida: tal vez los estándares de experiencia en nuestra industria son tan bajos que todo lo que tiene que hacer para superar radicalmente a todos los programadores de Rails que conoce es leer este libro, de principio a fin.

Sé que son más de 700 páginas, pero cada libro de A de Canción de Hielo y Fuego de George RR Martin es más extenso que eso, y sé que al menos la mitad de las personas que compraron este libro leerán esa serie completa. Si puedes sentarte a través de una interminable lista de pasteles de faisán y comida al azar que involucra caquis,

Prólogo iii

como si alguien siquiera supiera lo que es un caqui, puedes sentarte e invertir un tiempo en realmente ser bueno en lo que haces para ganarte la vida. Cosas más extrañas han sucedido.

Rails a menudo se ve como una abreviatura fácil de usar para principiantes para crear aplicaciones web. Las personas imprudentes dicen que hace que la creación de aplicaciones web sea tan simple que cualquiera pueda hacerlo. Voy a recomendar que consideres ese punto de vista como una completa mierda. Aquí hay un paradigma alternativo: Rails es una interfaz de usuario para el desarrollo web que simplifica las tareas complejas. No quita la complejidad. Agiliza su interacción con esa complejidad. Hay dos cosas que debes tener en cuenta aquí.

El primero es que en la programación, todas las abstracciones son, en última instancia, abstracciones con fugas. Por ejemplo, es irresponsable escribir cualquier tipo de código que maneje dinero y divisas si no comprende los conceptos básicos de matemática de coma flotante, incluidas las razones por las que las computadoras no pueden hacer matemática en coma flotante. Es literalmente imposible representar muchas fracciones decimales como fracciones binarias, y las computadoras representan todo en binario porque finalmente ejecutan todo a través de compuertas lógicas. Esto significa que es imposible representar con precisión ciertos valores numéricos como números flotantes, y las personas pueden tomar esa imposibilidad matemática de manera muy personal cuando afecta la precisión de los saldos de sus cuentas bancarias. En otras palabras, las realidades del cálculo en silicio son muy importantes cuando se realizan transacciones financieras. Es solo un ejemplo, pero en todos los casos, si tienes un sistema que simplifica las cosas complejas, sigue siendo una muy buena idea saber cuáles son esas complejidades subyacentes.

La segunda cosa que debes entender, para evitar la mayoría de las trampas en Rails, es que construir una buena interfaz de usuario toma mucho trabajo. Todos los proyectos que se encuentren allí encontrarán la difícil situación en la que sacarán el primer 90% del problema, y ​​luego lo descubrirán.

