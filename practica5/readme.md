# Práctica 5. Replicación de bases de datos MySQL.
En esta práctica vamos a crear una base de datos en una de nuestras máquinas (swap1) (maestro) y crear una réplica maestro-esclavo donde la otra máquina (swap2) hará de servidor backup (esclavo).

## Creación de la base de datos

Primero empezamos creando una base de datos MySQL (base de datos "contactos"). Además de crearla vamos a añadir una tabla ("datos") y un elemento en la tabla ("oscar" y su número de teléfono):
![img](https://raw.githubusercontent.com/widowert/swap/master/practica5/images/mysqlCreate1.png)

![img](https://raw.githubusercontent.com/widowert/swap/master/practica5/images/mysqlCreate2.png)

## Replicar base de datos MySQL con mysqldump

Para exportar la bse de datos que hemos creado en un archivo .sql, el cual podremos importar en otra máquina, solo tenemos que usar una sola orden (mysqldump) aunque por seguridad y para evitar que haya actualizaciones en la base de datos mientras estamos exportándola vamos a bloquearla y desbloquearla posteriormente.
![img](https://raw.githubusercontent.com/widowert/swap/master/practica5/images/mysqldump1.png)

![img](https://raw.githubusercontent.com/widowert/swap/master/practica5/images/mysqldump2.png)

Tras tener este archivo (/tmp/contactos.sql), el cual no es más que un archivo de texto plano con las ordenes necesarias para dejar una base de datos cualquiera con la tabla y datos que creamos anteriormente, vamos a importarlo a otra máquina (swap2), primero, necesitamos tener este archivo en la otra máquina, lo cual podemos hacerlo con scp, por ejemplo:

![img](https://raw.githubusercontent.com/widowert/swap/master/practica5/images/scp.png)

Una vez tenemos el archivo .sql, tenemos que crear una base de datos, ya que la importación no incluye la creación de una base de datos (para hacerlo seguimos los pasos del apartado anterior, solo crear la base de datos no añadir datos (create database x;)). Una vez creada ya podemos importar los datos:

![img](https://raw.githubusercontent.com/widowert/swap/master/practica5/images/mysqlImport.png)

>También podríamos haber importado directamente  usando un pipe a ssh (tendríamos que tener la base de datos creada) `mysqldump contactos -u root -p | ssh equipoDestino mysql`

## Replicar base de datos mediante configuración maestro-esclavo.

Partiendo del apartado anterior (teniendo la misma base de datos y tablas en ambas máquinas) vamos a empezar a configurar el maestro (swap1). Para ellos editamos el archivo /etc/mysql/mysql.conf.d/mysqld.cnf, en este comentamos la línea de "bind-address" (para escuchar a un servidor, en este caso localhost) y descomentamos "log-error" (para definir ruta del log de errores), "server-id" (para identificar este servidor entre los demás) y "log_bin" (para el registro binario con toda la información del registro de actualizaciones) si lo estuvieran y reiniciamos el servicio (service mysql restart), si no nos da ningún error podemos continuar.

Nos vamos al esclavo y repetimos la configuración anterior excepto el "server-id" que en este caso debe ser un número diferente (2) al del servidor maestro (1). Y reiniciamos mysql en esta máquina.

Si no nos han dado errores, nos vamos al maestro y vamos a crear un usuario, al cual le vamos a dar permiso para replicar nuestra base de datos, además de bloquear la base de datos para la correcta conexión maestro-esclavo.

![img](https://raw.githubusercontent.com/widowert/swap/master/practica5/images/mysqlMaster.png)

Ahora nos vamos al esclavo para darle los datos del maestro, vamos a definir la dirección ip de este, usuario y contraseña, archivos de log y posición (valores que se muestran en el maestro con "show master status;"), tras definirlo activamos el esclavo.

![img](https://raw.githubusercontent.com/widowert/swap/master/practica5/images/mysqlSlave.png)

Desbloqueamos las tablas en el maestro con "unlock tables;".

Para comprobar que se ha conectado al maestro y no hay errores ejecutamos "show slave status/g;" y el valor de "Seconds_Behind_Master" debe ser diferente de null (en mi caso es 0).

![img](https://raw.githubusercontent.com/widowert/swap/master/practica5/images/mysqlSlaveStatus.png)

Y finalmente para comprobar que todo funciona, vamos a hacer un cambio en la tabla datos en el maestro y comprobar que los cambios se guardan automáticamente en el esclavo:

![img](https://raw.githubusercontent.com/widowert/swap/master/practica5/images/mysqlChangeExample.png)

