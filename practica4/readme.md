# Asegurar la granja web

En esta práctica vamos a configurar ciertas medidas de seguridad para nuestra granaja web, estas medidas serán:
- Instalar un certificado SSL autofirmado para configurar el acceso por HTTPS a los servidores.
- Configurar el cortafuegos correctamente y hacer script de configuración.

# Instalar certificado SSL autofirmado en Apache.

## Instalación y configuración Apache
Vamos a seguir estrictamente los pasos del guión de prácticas y quedandonos en el nivel básico.
- Primero activamos el modulo SSL de Apache `a2enmod ssl`
- Reiniciamos Apache `service apache2 restart`
- Creamos la carpeta donde guardaremos el certificado y la llave de SSL `mkdir /etc/apache2/ssl`
- Generamos el certificado con la clave `openssl req -x509 -nodes -days 365 -newkey rsa:4048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt` a continuación le metemos todos los datos que nos vaya pidiendo para completar la ocnfiguración del dominio.
- Editamos el archivo de configuración del sitio de Apache `nano /etc/apache2/sites-available/default-ssl.conf` y agregamos un par de lineas debajo de donde pone `SSLEngine on`: añadimos `SSLCertificateFile rutaDelArchivo.crt` y `SSLCertificateKeyFile rutaDelArchivo.key`
- Activamos la configuración que acabamos de poner `a2ensite default-ssl`
- Y recargamos Apache `service apache2 reload`
>Ahora podemos comprobar que está correctamente hecho haciendo una petición desde nuestro host a esta máquina por HTTPS, por ejemplo con `curl -k https://ip/index.html`
![img](https://raw.githubusercontent.com/widowert/swap/master/practica4/images/curlHTTPS.png)

## Copiamos configuración a los demás servidores y configuramos.

Ahora que tenemos configurado el primer servidor y el certificado creado sólo tenemos que copiar este certificado a los demás servidores (para que todos sirvan con el mismo certificado por HTTPS). Para ello vamos a hacerlo de forma muy sencilla:
- Copiamos el certificado y la llave a nuestro servidor 2 mediante rsync
>![img](https://raw.githubusercontent.com/widowert/swap/master/practica4/images/copiaSSL.png)
Como se puede ver he copiado la carpeta ssl entera y los archivos de dentro.
- Ahora que tenemos todo sólo hay que repetir los pasos del apartado anterior saltándonos la generación de certificado.

## Configurando balanceador Nginx para HTTPS
Ahora solo nos falta configurar nuestra máquina balanceadora para que pueda redirigirnos por HTTPS (puerto 443) a los servidores. Para ello tenemos que:
- Copiar el certificado y clave a alguna carpeta cualquiera (mediante rsync por ejemplo)
- Modificar la configuración de Nginx en /etc/nginx/conf.d/default.conf y añadir en el apartador server: `listen 443 ssl;` `ssl on;` `ssl_certificate rutaDeArchivo.crt` `ssl_certificate_key rutaDeArchivo.key`
- Y reiniciamos Nginx.

# Configurando cortafuegos y haciendo script básico
En Ubuntu Server, según he comprobado en mi máquina, cuando nos referimos al firewall como servicio utilizamos `ufw`, `ufw enable/disable`,etc. Cuando queremos añadir o quitar reglas podemos hacerlo mediante `iptables` que modifica las tablas que utiliza `ufw` para su funcionamiento.
- Primero vamos a crear el script donde podemos ver algunos ejemplos de uso de iptables:
>![img](https://raw.githubusercontent.com/widowert/swap/master/practica4/images/copiaSSL.png)
Las dos primeras limpian las reglas que hubiera, las 3 siguientes establecen las políticas por defecto, las 2 siguientes permiten como salida las conexiones nuevas, establecidas y relacionadas y como entrada las establecidas y las relacionadas, las 2 ultimas permiten tanto de entrada como de salida los puertos de SSH, HTTP y HTTPS por tcp. 
- Ahora reiniciamos el servicio ufw `service ufw restart`
- Y activamos si no lo estuviera el ufw `ufw enable`
## Script al inicio del sistema
Otra actividad que pedía el guión es que configuremos el script para que se ejecute cada vez que iniciamos el sistema, para esto:
- Copiamos el script en /etc/init.d
- Le damos permisos de ejecución.
y actualizamos el rc.d con la configuración por defecto `sudo update-rc.d script.sh defaults` y listo.