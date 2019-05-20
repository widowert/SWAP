# Práctica 6. Servidor de disco NFS.
En esta práctica vamos a configurar un servidor de disco NFS para exportar espacio en disco a los clientes NFS. Esto servirá para tener una carpeta compartida entre servidores y clientes.

## Configuración del servidor NFS.

Primero necesitamos instalar todo lo necesario. Podemos hacerlo mediante apt: `apt-get install nfs-kernel-server nfs-common rpcbind`.
Ahora vamos a crear la carpeta que posteriormente vamos a compartir, en mi caso voy a crear "/shared", y le cambiamos de propietario y permisos.

![img](https://raw.githubusercontent.com/widowert/swap/master/practica6/images/sharedDir.png)

Lo siguiente es definir las máquinas clientes para darles permiso, para ello editamos el archivo /etc/exports y añadimos las IPs:

![img](https://raw.githubusercontent.com/widowert/swap/master/practica6/images/exports.png)

## Configuración de los clientes NFS.

Primero instalamos lo necesario: `apt-get install nfs-common rpcbind` en las dos máquinas clientes.
Luego, creamos la carpeta que será compartida y le damos permisos. Ya solo tendríamos que montarla y listo (hacer en las dos máquinas clientes).

![img](https://raw.githubusercontent.com/widowert/swap/master/practica6/images/sharedCli.png)

Para comprobar que funciona (que la carpeta es compartida y tenemos desde el cliente permisos de escritura) vamos a crear un archivo tanto en cliente como en servidor dentro de la carpeta y comprobaremos que los cambios se reflejan automáticamente en el otro.

![img](https://raw.githubusercontent.com/widowert/swap/master/practica6/images/test.png)

## Montado automático de carpeta compartida.

Como lo hemos hecho hasta ahora, tenemos una carpeta compartida entre cliente y servidor NFS, pero cada vez que reiniciemos el cliente tendremos que montar la carpeta compartida, por lo que sería de gran ventaja que el sistema la montara automáticamente al iniciarse. Para ello vamos a modificar el archivo /etc/fstab y vamos a añadir una línea justamente para esto:
(línea: 192.168.77.5:/shared /home/widowert/sharedCli/ nfs auto,noatime,nolock,bg,nfsvers=3,intr,tcp,actimeo=1800 0 0)

![img](https://raw.githubusercontent.com/widowert/swap/master/practica6/images/fstab.png)

Y como podemos ver la monta correctamente al iniciar: 

![img](https://raw.githubusercontent.com/widowert/swap/master/practica6/images/fstab2.png)