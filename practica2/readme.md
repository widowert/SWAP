# Práctica 2. Clonar información de un sitio web:

## Prueba de copia de archivos con ssh
>Para esta actividad he decidido usar rsync aunque no es necesario pues no vamos a copiar gran cantidad de datos. Podría haber utilizado la siguiente orden para tranferir el archivo:
`cat archivo | ssh usuario@ipEquipoDestino 'cat > rutaDestino'`

![img](https://raw.githubusercontent.com/widowert/swap/master/practica2/images/sshfile.jpg)

Quería cambiar la apariencia del bash de mis máquinas ubuntu server, para ello modifique el .bashrc en una de ellas y utilice rsync para copiarlo a la otra y tener la misma paleta de colores.



![img](https://raw.githubusercontent.com/widowert/swap/master/practica2/images/color2.jpg)

## Clonar una carpeta entre dos máquinas
Creo una carpeta en `~` llamada prueba y dentro un archivo prueba.txt que contiene la palabra "yes", esta carpeta la copio con rsync de la máquina swap2 a swap1 y compruebo que el contenido (mensaje txt) está correcto.

![img](https://raw.githubusercontent.com/widowert/swap/master/practica2/images/rsyncfinal.jpg)

## Configurar ssh sin contraseña
Primero generamos las claves ssh en ambas máquina con la orden ssh-keygen, luego solo tenemos que utilizar la orden ssh-copy-id contra la máquina que queramos que no nos pida la contraseña, esto nos servirá para poder automatizar el uso de ssh.
>Repetir en ambas máquinas para que ambas puedan conectarse entre sí sin petición de contraseña

![img](https://raw.githubusercontent.com/widowert/swap/master/practica2/images/sshkeygen.jpg)


![img](https://raw.githubusercontent.com/widowert/swap/master/practica2/images/sshcopyid.jpg)

## Establecer una tarea en Cron para mantener un directorio actualizado entre las dos máquinas

Con el uso de Cron y rsync podemos mantener una carpeta común entre dos máquinas actualizadas. Para ello nos vamos al archivo de Cron `/etc/crontab` y añadimos las ordenes que necesitemos, una en cada línea. Para mantener los cambios bidireccionalmente entre las máquinas deberíamos automatizar este proceso en ambas máquinas (añadir la misma línea de cron en ambas).

![img](https://raw.githubusercontent.com/widowert/swap/master/practica2/images/cronfile.jpg)
> La orden que he puesto se repetirá cada minuto (** ***), si quisiera que se repitiera cada hora podría poner (1 * ***), que lo repetiría cada 60 minutos (en el minuto 1 de cada hora) y a la otra máquina le podríamos poner (5 * ***) para que no lo hagan al mismo tiempo.

Voy a utilizar la carpeta que copie en el ejercicio anterior `~/prueba` la cual contiene un archivo prueba.txt que pone "yes", voy a cambiar el contenido en una máquina y vemos que sin hacer nada cambia el contenido en la otra.

![img](https://raw.githubusercontent.com/widowert/swap/master/practica2/images/cronresult.jpg)