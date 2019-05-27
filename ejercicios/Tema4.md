# Ejercicios Tema 4

## Buscar información sobre cuánto constaría en la actualidad un mainframe. Comparar precio y potencia entre esa máquina y una graja web de unas prestaciones similares.

No hay mucha información acerca de este tema y menos en cuanto a especificaciones, pero el precio de un Mainframe en los últimos años ronda los 100.000 euros aunque este precio es orientativo. Estos sistemas no se utilizan mucho actualmente, se tiende más a usar granjas ya que, como hemos visto en teoría, tienen más beneficios y menos problemas.

## Buscar infromación sobre precio y características de balanceadores hardware específicos. Compara las prestaciones que ofrecen unos y otros.

![img](https://raw.githubusercontent.com/widowert/swap/master/ejercicios/images/loadbalancerComp.png)

## Buscar información sobre los métodos de balanceo que implementan los dispositivos recogidos en el ejercicio 4.2.

Los dispositivos F5 tienen:
- Estático: Round-robin y ratio.
- Dinámico: por menos conexiones, más rapido, observado y predictivo.
- Mecanismos contra fallos: miembro de pool vs node, activación de grupo prioritario, host de reserva.

## Buscar información sobre métodos y herramientas para implementar GSLB.

Partiendo de tener dos sistemas, deberíamos crear una VPN para comunicarlos entre sí de forma segura. Luego tendremos que configurar un balanceador entre los clientes que tenga en cuenta la localización para redirigir a uno o a otro (al más cercano).

## Instala y configura en una máquina virtual el balanceador ZenLoadBalancer.

Primero nos descargamos la ISO del sistema basado en Debian GNU/Linux. Lo instalamos, rellenamos los datos que nos va pidiendo (datos para la configuración de red y del propio balanceador) y tras esto llevamos a cabo una rápida configuración, parecida a la configuración de nuestro balanceador en prácticas aunque podemos hacerlo de forma gráfica a través del servidor web del sistema.

## Probar las diferentes maneras de redirección HTTP. ¿Cuál es adecuada y cuál no lo es para hacer balanceo de carga global?

301 / 308: movida permanentemente, con esta opción todos los requests futuros tienen que redirigirse aquí. (Redirección permanente). (308 sin cambio de método HTTP).

302 / 307 : movida temporalmente, es una redirección puntual. (307 sin cambio de método HTTP).

Creo que los mejores serían 302 o 307, dependiendo de la funcionalidad que queramos. Estos son los temporales y la redirección para balanceo de carga es temporal ya que en una conexión puede redirigirte a un servidor y en la siguiente conexión a otro diferente, por lo que las redirecciones son temporales.

## Buscar información sobre los bloques de IP para los distintos países. Implementar en JavaScript o PHP la detección de la zona desde donde se conecta un usuario.

Podemos extraer en diferentes formatos la información sobre los diferentes bloques en esta página: www.countryipblocks.net.

Para implementar el uso de geolocalización con PHP podemos utilizar diferentes herrramientas ya desarrolladas como por ejemplo php-geoip que ya tiene scripts para buscar mediante diferentes métodos la localización, y no solo te devuelve esta, si no un Array con diferente información.
También podríamos usar alguna base de datos donde podríamos consultarlo.
