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
Y llegamos a lo que aprece ser una web en desarrollo con 2 enlaces. El primero te lleva a datos sobre Mercurio, que puedes ir cambiando si cambias el número de la URL. Hay hasta 8 datos. El segundo enlace es una lista de cosas que hacer para la web. <br>
El siguiente paso será usar una herramienta llamada SQLmap, para que liste bbdd.<br><br>

![Imagen2](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/78754d50-e547-4a22-a04c-b489aba5320e)
<br><br>

El comando nos buscará posibles vulnerabilidades sql en el servidor de la web y también nos listará las bases de datos disponibles en dicho servidor. <br><br>
![Imagen3](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/078871b4-79f8-408e-ad58-2c5eb3a9236f)
<br><br>

Estas son las bases de datos encontradas en el servidor: information_schema y mercury. <br>
Una vez encontradas las bases de datos, explotaremos la que nos interesa, en nuestro caso, la mercury. A continuación, seguiremos usando sqlmap pero esta vez para extraer información de la base de datos mercury: <br><br>
```
sqlmap -u http.//10.0.2.5:8080/mercuryfacts/ -D mercury --dump-all --batch
```
![Imagen4](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/a2bc1efa-acff-4bfa-b168-e24555a23e5b)
<br><br>

Y hemos obtenido una lista de usuarios con sus respectivas contraseñas. Sabiendo que el puerto 22, correspondiente a ssh, está abierto, vamos a intentar explotar esta vulnerabilidad probando cada usuario y contraseña. <br><br>

![Imagen5](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/9c681734-201f-43dc-9d72-ecabcfb608dd)
<br><br>

Finalmente, nos ha permitido acceder con el usuario webmaster. <br><br>

![Imagen6](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/d0979e8d-b046-444a-be3f-3c99fbb649f4)
<br><br>

A continuación, como hemos accedido como webmaster, listaremos los archivos y directorios en busca de algo interesante: <br><br>

![Imagen7](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/fcc207a5-4251-46bc-af88-830e929f3952)
<br><br>

Y hemos encontrado una flag. A continuación, seguiremos indagando en esta máquina en busca de más cosas. <br>
Veamos los derechos y privilegios del usuario con el que hemos accedido. 










