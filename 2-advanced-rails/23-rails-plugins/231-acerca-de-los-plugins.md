# 2.3.1- Acerca de los Plugins

### Carga de plugins

De forma predeterminada, los complementos se cargan desde los directorios en `vendor/plugins` en la raíz de la aplicación Rails. Si necesita cambiar o agregar a estas rutas, el elemento de configuración `plugin_paths` contiene las rutas de carga del complemento:

     `config.plugin_paths + = [File.join (RAILS_ROOT, 'vendor', 'other_plugins')]`

Por defecto, los complementos se cargan en orden alfabético; `attachment_fu` se carga antes de `http_authentication`. Si los complementos tienen dependencias entre sí, se puede especificar un orden de carga manual con el elemento de configuración de complementos:

    ` config.plugins =% w (prerrequisito_plugin actual_plugin)`

Cualquier complemento no especificado en `config.plugins` no se cargará. Sin embargo, si el último complemento especificado es el símbolo: all, Rails cargará todos los complementos restantes en ese punto. Rails acepta símbolos o cadenas como nombres de complementos aquí.

     `config.plugins = [: prerequisite_plugin,: actual_plugin,: all]`

El localizador de complementos busca complementos bajo las rutas configuradas, recursivamente. Debido a que se realiza una búsqueda recursiva, puede organizar complementos en directorios; por ejemplo, vendor / plugins / active\_record\_acts y vendor / plugins / view\_extensions.

El sistema real de localización y carga del complemento es extensible, y usted puede escribir sus propias estrategias. El localizador \(que de forma predeterminada es Rails :: Plugin :: FileSystemLocator\) busca complementos; el cargador \(por defecto Rails :: Plugin :: Loader\) determina si un directorio contiene un complemento y hace el trabajo de cargarlo.

### Instalación de plugins de Rails

Los complementos generalmente se instalan con la herramienta plugin integrada Rails, `script/plugin`. Esta herramienta de complemento tiene varios comandos:

`discover / source / unsource / sources`

La herramienta de complemento utiliza un método ad-hoc para encontrar complementos. En lugar de exigirle que especifique la URL de un repositorio de complementos, el script / plugin intenta encontrarlo por usted. Una forma de hacerlo es escaneando la página "Complementos" de la wiki de Rails \* para las URL de origen. Esto se puede activar con el comando descubrir.

Los comandos de origen y no fuente agregan y eliminan URL de origen, respectivamente. El comando de orígenes enumera todas las URL de origen actuales.

`instalar / actualizar / eliminar`

Estos comandos instalan, actualizan y desinstalan plugins. Pueden tomar una URL HTTP, una URL de Subversion \(svn: // o svn + ssh: //\), o un nombre de complemento desnudo, en cuyo caso se escanea la lista de fuentes.

La instalación de scripts / complementos tiene una opción, -x, que lo dirige a administrar complementos como Subversion externa. Esto tiene la ventaja de que el directorio aún está vinculado al repositorio externo. Sin embargo, es un poco inflexible: no puede seleccionar los cambios del repositorio en sentido ascendente. Examinaremos algunas mejores opciones más adelante.

