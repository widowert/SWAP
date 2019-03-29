# Práctica 1. Preparación de las herramientas:
Tras la instalación de 2 máquinas Ubuntu Server 16 en VirtualBox y la instalación en ellas de LAMP y SSH procedo a completar la práctica.

Cada máquina se llama SWAP1 y SWAP2 respectivamente. He creado una interfaz solo anfitrión para conectarlas entre sí y con el anfitrión. Sus ips son 192.168.77.3 (SWAP1) y 192.168.77.4 (SWAP2).
## Acceso por ssh de una máquina a otra
Mediante la orden ssh usuario@direccionIP nos conectamos como "usuario" a la máquina en dicha dirección ip, en mi caso es la conexión de SWAP2 a SWAP1.

![img](https://raw.githubusercontent.com/widowert/swap/master/practica1/images/ssh.jpg) 

## Acceso por curl de una máquina a otra
Para esta conexión solo he hecho una petición html al servidor Apache de una de mis máquina para que me devuelva la página que creamos al principio de la página (de SWAP1 a SWAP2).

![img](https://raw.githubusercontent.com/widowert/swap/master/practica1/images/curl.jpg)

* [Práctica 2](https://widowert.github.io/SWAP/practica2/) 

