# 2.5.4 - El ambiente de Ruby

Ningún análisis de la seguridad de Rails estaría completo sin examinar el entorno en el que vive Ruby.

### Usando el Shell

El método Kernel.system es útil para la interacción básica con los servicios del sistema a través de la línea de comandos. Sin embargo, al igual que con SQL, es importante asegurarse de saber exactamente qué se está aprobando, especialmente si proviene de una fuente externa.

La mejor forma de protegerse contra la entrada maliciosa del usuario al shell es usar la versión de sistema de múltiples parámetros, solo pasando el nombre del comando en el primer parámetro. Los parámetros subsiguientes se escapan y se pasan, lo que hace que sea mucho más difícil deslizar algo en la línea de comando desapercibido

### Obstaculización de objetos

Manchas es una idea que vino a Ruby desde Perl. Debido a que no se debe confiar en los datos que provienen del exterior, ¿por qué no obligarlos a que no se los confíe de manera predeterminada? Cualquier información leída del entorno o del mundo exterior está marcada como contaminada. Dependiendo del valor actual de un ruby ​​global especial, $ SAFE, ciertas operaciones están prohibidas en los datos viciados. Los objetos solo pueden ser \(oficialmente\) no contaminados llamando a su método no marcado.

Esta es una buena idea que, debido a los detalles de implementación, no ha ganado mucha tracción en la comunidad de Rails. Puede convertirse en un dolor para hacer frente a cada pieza de datos que se deriva de la entrada del usuario. Existe un plugin de Rails, safe\_erb, que aprovecha la contaminación para garantizar que todos los datos proporcionados por el usuario sean escapes de HTML antes de volver a mostrarse. Los parámetros de solicitud y las cookies se corrompen con cada solicitud, y se genera un error si se intenta procesar los datos contaminados. \(La instalación de contaminación de Ruby no se usa más que como una bandera en los objetos, porque cualquier cosa más requeriría un nivel $ SAFE mayor que cero, lo cual es Rails-antipático.\) Esto reduce la posibilidad de ataques de scripting entre sitios. El complemento está disponible en http: // agilewebdevelopment.com/plugins/safe\_erb.



