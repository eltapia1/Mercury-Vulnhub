# Informe vm Mercury-Vulnhub
## Pasos a seguir para resolver la vm Mercury de Vulnhub 

Primero de todo, escaneamos la red local en busca de direcciones IP. Para ello usaremos el comando arp-scan sobre la interfaz de red en la que estemos trabajando:
```
arp-scan -I eth3 --localnet --ignoredups
```
<br>
En mi caso, la IP de la máquina víctima es la 10.0.2.5. Le enviaremos un paquete para comprobar que está activa:
\```
ping -c1 10.0.2.5
\```
