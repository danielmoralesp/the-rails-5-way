# 2.6 - Performance

El rendimiento es una bestia interesante. La optimización del rendimiento a menudo tiene mala reputación porque a menudo se realiza demasiado temprano y con demasiada frecuencia, generalmente a expensas de la legibilidad, el mantenimiento y hasta la corrección. Rails es generalmente lo suficientemente rápido, pero es posible hacerlo lento si no tienes cuidado.

Debe tener en cuenta las siguientes pautas al optimizar el rendimiento:

**Las mejoras algorítmicas siempre superan los ajustes del código**

Es muy tentador tratar de extraer hasta el último bit de la velocidad de una pieza de código, pero a menudo se puede perder la imagen más grande. Ninguna cantidad de C o ajuste de conjunto hará que el burbujeo sea más rápido que el medio rápido. Comience su optimización de arriba hacia abajo.

**Como regla general, la facilidad de mantenimiento supera el rendimiento**

Su código debería ser primero fácil de leer y comprender, y solo luego optimizado para la velocidad.

**Solo optimice lo que importa**

Normalmente, el perfil del código tiene una distribución desequilibrada: el 80% del tiempo se gasta en el 20% del código \(para un valor de 80% y 20%\). Tiene sentido gastar sus recursos limitados optimizando las secciones que traerán la mayor ganancia en el rendimiento.

**Mida dos veces, corte una vez**

La única manera de estar seguro de dónde gasta su tiempo el código es medirlo. Y al igual que en la carpintería, puede perder mucho tiempo si realiza cambios sin estar muy seguro de cuáles deberían ser esos cambios. En este capítulo, exploraremos algunas de las mejores herramientas y métodos para determinar dónde cortar.



