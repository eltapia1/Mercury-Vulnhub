# Informe vm Mercury-Vulnhub
A continuación, el walkthrough de la máquina virtual Mercury, del grupo Planets de Vulnhub, explicado paso a paso.
## Fase de reconocimiento 

Primero de todo, escaneamos la red local en busca de direcciones IP. Para ello usaremos el comando arp-scan sobre la interfaz de red en la que estemos trabajando: <br><br>
```
arp-scan -I eth3 --localnet --ignoredups
```
![Captura de pantalla 2024-01-23 180113](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/7410790a-b735-47ee-a640-2f05364c6852)

<br><br>
En mi caso, la IP de la máquina víctima es la 10.0.2.5. Le enviaremos un paquete para comprobar que está activa: <br><br>
```
ping -c1 10.0.2.5
```
<br>
Si se ha recibido el paquete podemos confirmar que nuestra máquina está activa.<br><br>

![Captura de pantalla 2024-01-23 180235](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/d0ec0854-270f-4e5f-af51-eca55140a64f)

A continuación, realizaremos un escaneo de puertos, buscando cuales estan abiertos con nmap <br><br>
```
nmap -p- --open -sS --min-rate 5000 -n -Pn 10.0.2.5 -oG allports
```
![Captura de pantalla 2024-01-23 183345](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/e4719d30-0384-4c9b-bff6-e6f762df49e6)
<br>

Como podemos comprobar, tiene abiertos los puertos 22 y 8080, que corresponden a ssh y http-proxy respectivamente. <br>
## Conexion e investigación de la máquina
Lo siguiente que haremos será conectarnos a la IP a través del puerto 8080 en un navegador. <br><br>

![Captura de pantalla 2024-01-23 183834](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/2415de39-a745-471f-89f9-276249a71a6f)
<br><br>

Vemos que nos aparece un mensaje, así que seguiremos investigando esta web. <br><br>

Lo siguiente que deberíamos hacer sería lanzar un Gobuster en busca de directorios en el servidor de la web. Pero antes de ello, iremos directamente a ver el robots.txt, a ver si ahí encontramos algo y nos podemos ahorrar el Gobuster. <br><br>

![Captura de pantalla 2024-01-23 185208](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/ad4a1645-07c3-460c-92e1-3267378ba98e)
<br><br>

Probaremos de usar el asterisco como directorio: <br><br>

![Captura de pantalla 2024-01-24 092445](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/b00d3310-a9cd-4f74-9aab-786979fe1ec2)
<br><br>

De lo que vemos en la web, lo más interesante son los 3 puntos que aparecen. Y si nos fijamos en el punto 3, tiene pinta de ser un directorio. Probaremos a ver. <br><br>
![Captura de pantalla 2024-01-24 093452](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/5390e909-9cb6-4d86-bc4e-cdaba241cc26)
<br><br>








