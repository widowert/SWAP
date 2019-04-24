# Práctica 3. Balanceo de carga:

## Estructura de red

Para esta práctica tenemos que modificar un poco el esquema de red que teníamos anteriormente en la práctica 2. 

>![img](https://raw.githubusercontent.com/widowert/swap/master/practica3/images/networkSqueme.png)
>
> Nuevo esquema de red.

Vamos a añadir una nueva máquina intermedia que funcione como balanceador de carga (m3-b) , esta máquina será Ubuntu Server (un máquina virtual) y balanceará la carga mediante software (Nginx).
>En mi caso, para ahorrar tiempo, voy a clonar una de las máquinas (prácticamente identicas) que tenía y usaré esta nueva clonada de balanceador.
>
>Es importante aclarar que para utilizar Nginx debemos de tener libre el puerto 80. En mi caso he clonado una máquina con Apache2 por lo que antes de instalar Nginx lo desinstalo (o desactivo) y compruebo que no tengo procesos ocupando este puerto ( `netstat –tulpn | grep :80` ), además de editar el archivo interfaces, ya que pusimos una ip estática manualmente y al clonar tendríamos la misma ip repetida. Quedaría así:
>![img](https://raw.githubusercontent.com/widowert/swap/master/practica3/images/networkSqueme2.png)
>

# Nginx
Instalamos Nginx con `apt-get install nginx`.
Para la configuración básica de Nginx, nos vamos a /etc/nginx/conf.d/default.conf (este archivo puede no existir, lo crearemos en tal caso, en otro caso lo borraremos para hacerlo de 0). Nuestro archivo quedará de la siguiente manera: 
![img](https://raw.githubusercontent.com/widowert/swap/master/practica3/images/nginxConf1.png)

> Además podemos definir los pesos de los servidores, es decir, la cantidad de carga que le será redireccionada, suponiendo que el que reciba más carga es un máquina más potente.
> Por ejemplo, para que un servidor tenga el doble de carga: 
> `upstream apaches {`
> 
	>> `server 192.168.77.3 weight=1;`
	>
	>> `server 192.168.77.4 weight=2;`
	>
> `}`

Tras esta configuración todo podría funcionar correctamente tras el reinicio del servicio Nginx, aunque puede no funcionar el balanceo, esto se debe a una directiva en el archivo de configuración (/etc/nginx/nginx.conf) que debemos comentar (esta directiva configura nginx como servidor web y no como balanceador):
![img](https://raw.githubusercontent.com/widowert/swap/master/practica3/images/nginxConf2.png)
