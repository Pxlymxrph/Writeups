# MoneyBox - Walkthrought

URL → https://www.vulnhub.com/entry/moneybox-1,653/

Este desafío CTF de nivel fácil tiene como objetivo encontrar las tres banderas ocultas y obtener acceso al usuario root de la máquina

![20.png](images/20.png)

![21.png](images/21.png)

1. El primer paso es ejecutar el comando `arp-scan` para identificar la dirección IP de la máquina víctima. Esto muestra una lista de las direcciones disponibles e identifico la IP destino (PCS Systemtechnik GmbH) con dirección 192.168.1.78. Con `curl` obtengo el valor del ttl y se que se trata de un sistema Linux
    - `arp-scan -I eth0 --localnet --ignoredups`
    - `curl -c 1 192.168.1.78`

![1.png](images/1.png)

1. Uso nmap para descubrir los puertos abiertos y sus respectivas versiones, teniendo como respuesta que los puertos 21, 22 y 80 se encuentran abiertos. Al analizar detenidamente el resultado de nmap observo que el login anonimo de ftp esta abierto
    - `nmap -p- -sS --min-rate 5000 192.168.1.78 -Pn -n -vvv`
    - `nmap -sCV 192.168.1.78 -p21,22,80`
    
    ![1.2.png](images/1.2.png)
    

![2.png](images/2.png)

1. Me apoyo de `whatweb` para identificar las tecnologías que corren detrás del servicio http. Con  `curl` previsualizo la página web
    - `whatweb http://192.168.1.78/`
    - `curl "http://192.168.1.78/"`

![3.png](images/3.png)

1. Ingreso al puerto 80 y visualizo el mensaje del presunto hacker que comprometió el servicio. Analizo la página sin encontrar nada interesante por lo que hago uso de `dirsearh` para hacer fuzzing rápidamente, encontrando el directorio /blogs/. Busco el código fuente de la página y encuentro una nota al final con un nuevo directorio

![4.png](images/4.png)

![5.png](images/5.png)

1. Inspeccionando nuevamente el código fuente logro encontrar una supuesta contraseña la cual guardo para usarla después

![6.png](images/6.png)

1. Sin nada más en la web, procedo a loguearme anonimamente en el servicio ftp y encuentro una imagen con un titulo muy interesante que nos invita a analizarla mas a fondo, por lo cual la traemos a nuestra máquina
    - `ftp 192.168.1.78`
    - `get trytofind.jpg`

![7.png](images/7.png)

1. Dispongo de `exiftool` y `stegseek` sin encontrar información relevante; procedo a analizarla con `steghide` para saber si se ha usado esteganografía y logro obtener un mensaje junto con un posible usuario (renu)

![9.1.png](images/9.1.png)

1. Ahora que tengo un posible usuario hago uso de `hydra` para aplicar fuerza bruta mediante el servicio ssh, logrando obtener las credenciales válidas
    - `hydra -l renu -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.78`

![10.png](images/10.png)

1. Una vez logueado como el usuario renu puedo leer la primer flag. Aplico una búsqueda en el /etc/passwd para identificar mas usuarios disponibles
    - `cat /etc/passwd | grep "/bin/bash"`
            
        ![11.png](images/11.png)
            
        
        ![12.png](images/12.png)
        

1. Me dirijo al directorio principal del usuario lily e intento leer el .bash_history donde puedo ver que siendo el usuario renu, tengo posibilidad de convertirme en lily mediante el id_rsa. Realizo el pivoting y leo la segunda flag
    - `ssh lily@localhost -i .ssh/id_rsa`

![22.png](images/22.png)

![13.png](images/13.png)

![15.png](images/15.png)

1. Busco binarios que permitan a usuarios sin privilegios ejecutar comandos como superusuario, encontrando perl. Con ayuda de GTFObins identifico una vía potencial de escalar privilegios
    - `sudo -l`
    - `sudo perl -e 'exec "/bin/bash";'`

![17.png](images/17.png)

![16.png](images/16.png)

1. Una vez siendo el usuario root, procedo a leer la última flag y dar por terminada la maquina

![18.png](images/18.png)

