# Informe vm Mercury-Vulnhub
## Pasos a seguir para resolver la vm Mercury de Vulnhub 

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
Lo siguiente que haremos será conectarnos a la IP a través del puerto 8080 en un navegador. <br><br>
![Captura de pantalla 2024-01-23 183834](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/2415de39-a745-471f-89f9-276249a71a6f)
<br><br>
Vemos que nos aparece un mensaje, así que seguiremos investigando esta web. <br><br>
Lo siguiente que deberíamos hacer sería lanzar un Gobuster en busca de directorios en el servidor de la web. Pero antes de ello, iremos directamente a ver el robots.txt, a ver si ahí encontramos algo y nos podemos ahorrar el Gobuster. <br><br>
![Captura de pantalla 2024-01-23 185208](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/ad4a1645-07c3-460c-92e1-3267378ba98e)

