# 3- Ajax on Rails

Ajax es un acrónimo que significa JavaScript asincrónico y XML. Abarca técnicas que nos permiten animar páginas web con comportamientos que ocurren fuera del ciclo de vida de solicitud HTTP normal \(sin una actualización de página\).

Algunos casos de uso de ejemplo para las técnicas de Ajax son:

* Sugerencia de entrada "Escribir anticipadamente", como en la búsqueda de Google.
* Envío de datos del formulario de forma asincrónica.
* Navegación sin interrupciones de mapas presentados en la web, como en Google Maps.
* Listas y tablas actualizadas dinámicamente, como en Gmail y otros servicios de correo electrónico basados ​​en la web. • Hojas de cálculo basadas en la web.
* Formularios que permiten la edición in situ.
* Vista previa en vivo de la escritura formateada junto con una entrada de texto.

Ajax es posible gracias al XMLHttpRequestObject \(o XHR, para abreviar\), una API que está disponible en todos los navegadores modernos. Permite que el código JavaScript en el navegador intercambie datos con el servidor y lo use para cambiar la interfaz de usuario de su aplicación sobre la marcha, sin necesidad de actualizar la página.

A propósito, Ajax, especialmente en Rails, tiene muy poco que ver con XML, a pesar de su presencia allí al final de las siglas. Por defecto, Rails ni siquiera incluye capacidades de análisis XML. La carga útil de las solicitudes asincrónicas puede ser cualquier cosa. A menudo son simplemente parámetros de formulario intercambiados por fragmentos de HTML, insertados dinámicamente en el DOM de la página. Lo más común actualmente es que el cliente y el servidor hablen entre sí utilizando datos codificados en una variante simple de JavaScript llamada JSON.

Está fuera del alcance de este libro enseñarle los fundamentos de JavaScript y / o Ajax. También está fuera de nuestro alcance profundizar en las consideraciones de diseño de agregar Ajax a su aplicación, elementos de los cuales son largos y ocasionalmente controvertidos. La cobertura adecuada de esos temas requeriría un libro completo y hay muchos de esos libros para elegir en el mercado. Por lo tanto, el resto del capítulo supondrá que usted comprende qué es Ajax y por qué lo usaría en sus aplicaciones. También supone que tiene una comprensión básica de la programación de JavaScript.

> CoffeeScript
>
> CoffeeScript es un lenguaje Ruby-esque que se compila en JavaScript. Debido a que DHH y muchos desarrolladores de Rails son fanáticos de CoffeeScript, el soporte para usarlo \(incluido el compilador\) se incluye de forma nativa en Rails. Este libro supone que el lector está familiarizado con la sintaxis de CoffeeScript.

### JavaScript discreto

Las características de JavaScript discreto \(UJS\) en Rails proporcionan una API independiente de la biblioteca para especificar las acciones de Ajax. El equipo de Rails ha proporcionado implementaciones de UJS para jQuery y Prototype, disponibles en https://github.com/rails/jquery-ujs y https://github.com/rails/prototype-rails, respectivamente. De forma predeterminada, las aplicaciones Rails recién creadas usan jQuery como su biblioteca de JavaScript de elección.

Para integrar jQuery fácilmente en su aplicación Rails, simplemente incluya la gema jquery-rails en su Gemfile y ejecute la instalación del paquete. A continuación, asegúrese de que las directivas correctas estén presentes en su archivo de manifiesto de JavaScript \(se enumeran a continuación\).

The Rails 5 Way

\# Gemfile

```
gem 
'jquery-rails'

```

```
// app/assets/javascripts/application.js
//= require jquery
//= require jquery_ujs

```

Al incluir las que requieren declaraciones en el archivo de manifiesto de JavaScript, las bibliotecas jQuery y jquery\_-ujs se incluirán automáticamente junto con el resto de sus activos y se mostrarán al navegador de manera eficiente. El uso de archivos de manifiesto se trata en detalle en el Capítulo 20, "Canalización de activos".

### Uso de UJS

Antes de la versión 3.0, Rails no era discreto, lo que resulta en un marcado generado que se acopla a su biblioteca de JavaScript de su elección. Por ejemplo, uno de los cambios más dramáticos causados por el cambio a UJS fue la forma en que se generaron los enlaces de eliminación.

```
=link_to 'Delete', user_path(1), method: :delete,
    data: { confirm: "Are you sure?" }
```

Antes del uso de las técnicas de UJS, el HTML resultante se parecería a este desastre:

```
<a href="/users/1" onclick="if (confirm('Sure?')) { var f = document.createElement('form'); f.style.display = 'none'; this.parentNode.appendChild(f); f.method = 'POST'; f.action = this.href;var m = document.createElement('input'); m.setAttribute('type', 'hidden'); m.setAttribute('name', '_method'); m.setAttribute('value', 'delete'); f.appendChild(m);f.submit(); };return false;">Delete</a>
```



