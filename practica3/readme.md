# Práctica 3. Balanceo de carga:

## Estructura de red

Para esta práctica tenemos que modificar un poco el esquema de red que teníamos anteriormente en la práctica 2. 

>![img](https://raw.githubusercontent.com/widowert/swap/master/practica3/images/networkSqueme.png)
>
> Nuevo esquema de red.

Vamos a añadir una nueva máquina intermedia que funcione como balanceador de carga (m3-b) , esta máquina será Ubuntu Server (un máquina virtual) y balanceará la carga mediante software (Nginx y Haproxy).

>En mi caso, para ahorrar tiempo, voy a clonar una de las máquinas (prácticamente identicas) que tenía y usaré esta nueva clonada de balanceador.
>
>Es importante aclarar que para utilizar Nginx debemos de tener libre el puerto 80. En mi caso he clonado una máquina con Apache2 por lo que antes de instalar Nginx lo desinstalo (o desactivo) y compruebo que no tengo procesos ocupando este puerto ( `netstat –tulpn | grep :80` ), además de editar el archivo interfaces, ya que pusimos una ip estática manualmente y al clonar tendríamos la misma ip repetida. Quedaría así:
>
>![img](https://raw.githubusercontent.com/widowert/swap/master/practica3/images/networkSqueme2.png)
>

# Nginx

>Nginx es un servidor web ligero de alto rendimiento que también puede ser usado como balanceador de carga entre otras utilidades.

Una vez tengamos nuestra máquina balanceadora lista instalamos Nginx con `apt-get install nginx`.

## Configuración básica

Para la configuración básica de Nginx, nos vamos a **/etc/nginx/conf.d/default.conf** (este archivo puede no existir, lo crearemos en tal caso, en otro caso lo borraremos para hacerlo de 0). Nuestro archivo quedará de la siguiente manera: 

![img](https://raw.githubusercontent.com/widowert/swap/master/practica3/images/nginxConf1.png)

> Además podemos definir los pesos de los servidores, es decir, la cantidad de carga que le será redireccionada, suponiendo que el que reciba más carga es un máquina más potente.
> Por ejemplo, para que un servidor tenga el doble de carga: 
`upstream apaches {
	server 192.168.77.3 weight=1;
	server 192.168.77.4 weight=2;	
 }`

Tras esta configuración todo podría funcionar correctamente tras el reinicio del servicio Nginx, aunque puede no funcionar el balanceo, esto se debe a una directiva en el archivo de configuración **/etc/nginx/nginx.conf** que debemos comentar (esta directiva configura nginx como servidor web y no como balanceador):

![img](https://raw.githubusercontent.com/widowert/swap/master/practica3/images/nginxConf2.png)

## Configuración de balanceo por IP

Tal y como lo tenemos ahora nuestro servidor balanceará la carga en partes iguales entre los dos servidores disponibles, pero si en nuestro sitio web usamos alguna información que haya que mantener entre página y página ( sesiones o algún tipo de estado) nuestro balanceador podría llevar a la misma IP hacia servidores diferentes en diferentes consultas, por lo que esta información se perdería. Para solucionarlo le decimos a nuestro balanceador que balancee fijándose en las IP's, para que un misma IP siempre vaya al mismo servidor.

Para llevar a cabo esta modificación solo tenemos que añadir en nuestro archivo "default.conf" en el "upstream" la directiva `ip_hash`:

`upstream apaches {
	ip_hash;
	server 192.168.77.3 weight=1;
	server 192.168.77.4 weight=2;
 }`
 
 >La solución moderna es el balanceo por cookies. Las IP's no siempre son únicas ya que las redes tras un proxy o NAT compartirán IP pública y esto puede generar un desequilibrio en el balanceo de carga.

## Configuración de balanceo con persistencia

También podemos decirle a Nginx que cuando se abra una conexión mantenga esta abierta cierto tiempo, de esta forma cuando venga otra petición dentro de dicho tiempo no se hará uso de balanceo ya que la conexión ya estaría establecida. 

Para llevar a cabo esta modificación solo tenemos que añadir en nuestro archivo "default.conf" (a partir de Nginx 1.2) en el "upstream" la directiva `keepalive <time>` y durante "time" segundos se mantendrá la conexión:

`upstream apaches {
	server 192.168.77.3 weight=1;
	server 192.168.77.4 weight=2;
	keepalive 5;
 }`

# Haproxy

>Haproxy es un balanceador de carga y también un servidor proxy. Es rápido y eficiente además de poder balancear cualquier tipo de tráfico.

En esta práctica vamos a usar Haproxy como sustituto de Nginx y, posteriormente, comparar cual hace mejor su trabajo en este caso.  En este apartado hablamos de la instalación y configuración de Haproxy.

>Para simplificar voy a utilizar la misma máquina que he usado para Nginx, nuestro balanceador. Lo único que tendré que hacer es desactivar el servicio que no vaya a utilizar y activar el que si.

Para instalarlo podemos utilizar Apt: `apt-get install haproxy`. Una vez instalado vamos a llevar a cabo una configuración básica que encaje con nuestro esquema y nuestros servicios. 

En **/etc/haproxy/haproxy.cfg** definimos la configuración necesaria, en nuestro caso, como necesitamos una configuración muy simple, voy a borrar la configuración por defecto y pondremos la siguiente:

![img](https://raw.githubusercontent.com/widowert/swap/master/practica3/images/haproxyConf1exp.png)

>Para comprobar que funciona tanto con nginx como con haproxy desde el anfitrión podemos usar `curl` para checkear que realmente nos redirecciona a un servidor diferente en cada conexión. En mi caso, para ver la diferencia, he puesto la dirección del servidor que sirve la página en el título (html) (las comprobaciones pedidas en el guión están al final):

![img](https://raw.githubusercontent.com/widowert/swap/master/practica3/images/balancerCurl.png)

# Benchmarking: comparando los balanceadores

En este apartado vamos a usar la utilidad Apache Benchmark para comparar el rendimiento de los dos balanceadores software que hemos utilizado.
Para instalar Apache Benchmark mediant Apt: `apt-get install apache2-utils` y para su ejecución: `ab -n <numero de peticiones> -c <numero de peticiones concurrentemente> <página a pedir>`, yo he utilizado `ab -n 100000 -c 10 http://192.168.77.5/index.html`.

![img](https://raw.githubusercontent.com/widowert/swap/master/practica3/images/htopReposo.png)
> Captura del estado de las máquinas en reposo con Htop.

## Benchmarking Nginx

![img](https://raw.githubusercontent.com/widowert/swap/master/practica3/images/htopNginxAb2.png)

![img](https://raw.githubusercontent.com/widowert/swap/master/practica3/images/nginxAbResult.png)

## Benchmarking Haproxy

![img](https://raw.githubusercontent.com/widowert/swap/master/practica3/images/htopHaproxyAb2.png)

![img](https://raw.githubusercontent.com/widowert/swap/master/practica3/images/haproxyAbResult.png)

## Comparación

Como podemos ver en los resultados de los benchmarks ambos balanceadores tienen tiempos muy similares, de hecho, la mayoría son idénticos. En cuanto a la pequeña diferencia que hay entre ambos siempre haproxy está unos ms por debajo, por lo que podríamos afirmar que Harpoxy es mejor en este caso y con la configuración tan básica que hemos utilizado.

# Capturas extra de comprobación

## Nginx normal

![img](https://raw.githubusercontent.com/widowert/swap/master/practica3/images/checkNginxNormal.png)

## Nginx con ponderación

![img](https://raw.githubusercontent.com/widowert/swap/master/practica3/images/checkNginxPesos.png)

## Haproxy normal

![img](https://raw.githubusercontent.com/widowert/swap/master/practica3/images/checkHaproxyNormal.png)

## Haproxy con ponderación

![img](https://raw.githubusercontent.com/widowert/swap/master/practica3/images/checkHaproxyPesos.png)
