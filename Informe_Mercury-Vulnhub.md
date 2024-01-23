# Informe vm Mercury-Vulnhub
## Pasos a seguir para resolver la vm Mercury de Vulnhub 

Primero de todo, escaneamos la red local en busca de direcciones IP. Para ello usaremos el comando arp-scan sobre la interfaz de red en la que estemos trabajando: <br><br>
```
arp-scan -I eth3 --localnet --ignoredups
```
<br><br>
En mi caso, la IP de la máquina víctima es la 10.0.2.5. Le enviaremos un paquete para comprobar que está activa: <br><br>
```
ping -c1 10.0.2.5
```
<br><br>
Si se ha recibido el paquete podemos confirmar que nuestra máquina está activa.
A continuación, realizaremos un escaneo de puertos, buscando cuales estan abiertos con nmap <br><br>
```
nmap -p- --open -sS --min-rate 5000 -n -Pn 10.0.2.5 -oG allports
```
<br><br>
Como podemos comprobar, tiene abiertos los puertos 22 y 8080, que corresponden a ssh y http-proxy respectivamente. <br>
Lo siguiente que haremos será conectarnos a la IP a través del puerto 8080 en un navegador. Vemos que nos aparece un mensaje. Seguiremos investigando esta web. Lo siguiente que deberíamos hacer sería lanzar un Gobuster en busca de directorios en el servidor de la web. Pero antes de ello, iremos directamente a ver el robots.txt, a ver si ahí encontramos algo y nos podemos ahorrar el Gobuster.

