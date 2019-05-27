# Ejercicios Tema 3

## Buscar con qué órdenes de terminal o herramientas podemos configurar bajo Windows y bajo Linux el enrutamiento del tráfico de un servidor para pasar el tráfico desde una subred a otra.

Linux:

`route -p add -net network-address -gateway gateway-address`
- -p :
Crea una ruta que debe persistir tras el reinicio del sistema. Si desea que la ruta sea válida sólo para la sesión actual, no utilice la opción  **-p**.

Windows:

`route ADD <Red de destino> MASK <mascara de subred> <Gateway>`

## Busca con qué ordenes de terminar o herramientas gráficas podemos configurar bajo Windows y bajo Linux el filtrado y bloqueo de paquetes.

Linux:

Contamos con la utilidad iptables (no gráfica), con la cual podemos definir diferentes reglas sobre diferentes redes, puerto y/o protocolos.

Windows:

Con el firewall del sistema o Windows Defender Firewall (gráfica) podemos llevar a cabo esta funcionalidad.