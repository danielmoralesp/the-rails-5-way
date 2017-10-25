# 2.4.6 - Equilibrio de carga y alta disponibilidad

Muchas aplicaciones requieren alguna forma de equilibrio de carga y / o alta disponibilidad. Aunque estos términos se usan a menudo juntos y a menudo se pueden obtener por los mismos métodos, son fundamentalmente dos requisitos diferentes. Los definimos así:

#### Balanceo de carga

La demanda de propagación se carga en varios sistemas para reducir la carga en un solo sistema.

#### Alta disponibilidad

Resistencia a la falla de uno o varios componentes constituyentes; la capacidad de continuar brindando servicios sin interrupción a pesar de la falla del componente.

Estas son cosas completamente diferentes, pero a menudo se requieren y / o se proporcionan juntas. Es importante entender la diferencia entre ellos para analizar adecuadamente los requisitos de una aplicación. Es posible proporcionar balanceo de carga sin alta disponibilidad; por ejemplo, considere un grupo de servidores presentados a Internet mediante DNS round-robin. La carga se distribuye más o menos por igual en el grupo de servidores, ¡pero el sistema no está muy disponible! Si un servidor deja de funcionar, DNS seguirá distribuyéndole fielmente las solicitudes, y todas las N solicitudes quedarán sin respuesta.

Por el contrario, se puede proporcionar alta disponibilidad sin balanceo de carga. La alta disponibilidad requiere el uso de componentes redundantes, pero nada dice que esos componentes deben estar en línea y en uso. Una configuración común es el repuesto dinámico: un servidor duplicado que se mantiene encendido pero fuera de línea, monitoreando continuamente su gemela en línea, listo para asumir el control si es necesario. Esto en realidad puede ser más económico que tratar de equilibrar las solicitudes entre los dos servidores y mantenerlos sincronizados.

En esta sección, revisamos las soluciones principales de equilibrio de carga y alta disponibilidad para sistemas de administración de bases de datos comunes.

### PostgreSQL

Hay varias opciones de balanceo de carga y alta disponibilidad para PostgreSQL. Debido a que no existe una sola compañía detrás de PostgreSQL, las opciones son provistas por diferentes organizaciones y compañías. Cada producto generalmente incorpora una replicación diferente o paradigma de agrupamiento. Algunas de las opciones se describen en esta sección.

#### Alta disponibilidad: Warm standby

Warm standby es una forma simple de lograr una alta disponibilidad en PostgreSQL. Requiere cierta configuración, pero la configuración está bien documentada. Warm standby utiliza el registro de escritura anticipada \(WAL\) en el que PostgreSQL registra la actividad. Los cambios se escriben en el WAL antes de comprometerse, por lo que el estado de la base de datos se puede reconstruir incluso si una transacción se interrumpe catastróficamente. El envío de registros es el proceso de enviar el WAL como archivos desde el maestro a un esclavo.

En una configuración de espera activa, un servidor está en modo de espera, en modo de restauración. Continuamente se está restaurando desde el servidor primario, utilizando un comando de restauración que espera que los WAL estén disponibles y los aplica tan pronto como lo hacen. Si el servidor primario muere, un sistema de supervisión \(que debe ser proporcionado por el usuario\) designa el modo de espera como el nuevo servidor primario.

#### Replicación maestro-esclavo: Slony-I

Slony-I es un sistema de replicación maestro-esclavo similar a los mecanismos de replicación incluidos con MySQL. Es compatible con la promoción de esclavos a maestros, pero, al igual que MySQL, no proporciona ningún mecanismo para detectar que los nodos han fallado.

Una actualización a Slony, Slony-II, se encuentra en las primeras etapas de desarrollo ahora. Tiene previsto proporcionar una réplica sincrónica multimaestro para PostgreSQL basada en el marco de comunicación de grupo Spread.

#### Replicación Multimaster: PGCluster

PGCluster \(http://pgcluster.projects.postgresql.org/\) es un producto que ofrece la replicación de varios y la agrupación para PostgreSQL. Proporciona equilibrio de carga y alta disponibilidad para un clúster de base de datos. El software maneja el failover y proporciona una solución fácilmente disponible si se utilizan tres o más servidores físicos.

El estilo de replicación de PGCluster es sincrónico; las actualizaciones se propagan a todos los servidores antes de que la transacción de actualización tenga éxito. Por lo tanto, solo se debe usar en entornos donde todos los servidores maestros estén en la misma ubicación y estén siempre conectados. La replicación asincrónica, en la que los cambios se propagan a otros servidores poco tiempo después de la transacción, generalmente se considera un problema difícil. La replicación asincrónica también es específica de la aplicación, ya que la forma correcta de manejar los conflictos entre dos transacciones confirmadas depende de las necesidades de la aplicación.

