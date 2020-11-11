# Autentificación, Autorización y Control de Acceso

* **Tarea 6 (1 punto)(Obligatorio): Añade al escenario otra máquina conectada por una red interna al servidor. A la URL departamentos.iesgn.org/intranet sólo se debe tener acceso desde el cliente de la red local, y no se pueda acceder desde la anfitriona por la red pública. A la URL departamentos.iesgn.org/internet, sin embargo, sólo se debe tener acceso desde la anfitriona por la red pública, y no desde la red local.**

1. Creamos la máquina (cliente) , cuya ip flotante es: **172.22.200.148**

![instancias.png](https://github.com/CeliaGMqrz/servidor_Nginx/blob/main/capturas/instancias.png)

3. Creamos los directorios internet e intranet con un index.html

```sh
mkdir /srv/www/departamentos/intranet
mkdir /srv/www/departamentos/internet
cp /srv/www/iesgn/principal/index.html /srv/www/departamentos/intranet/
cp /srv/www/iesgn/principal/index.html /srv/www/departamentos/internet/
nano /srv/www/departamentos/internet/index.html 
nano /srv/www/departamentos/intranet/index.html 
```

2. Configuramos el servidor en nuestro virtualhost departamentos y el */etc/hosts* en el cliente

En el **sevidor**:

```sh
nano /etc/nginx/sites-available/departamentos 
```

```sh
# Default server configuration

server {
        listen 80;

        root /srv/www/departamentos;

        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;

        server_name departamentos.iesgn.org;

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }

        location /intranet {
                allow 10.0.0.6/24;
                deny all;
        }
}

```

En el **cliente**:

```sh
nano /etc/hosts
```

```sh
# Your system has configured 'manage_etc_hosts' as True.
# As a result, if you wish for changes to this file to persist
# then you will need to either
# a.) make changes to the master file in /etc/cloud/templates/hosts.debian.tmpl
# b.) change or remove the value of 'manage_etc_hosts' in
#     /etc/cloud/cloud.cfg or cloud-config from user-data
#
127.0.1.1 cliente-nginx.novalocal cliente-nginx
127.0.0.1 localhost

10.0.0.5 departamentos.iesgn.org
# The following lines are desirable for IPv6 capable hosts
::1 ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
ff02::3 ip6-allhosts

```


3. Comprobamos que no podemos acceder desde el exterior (con nuestra máquina física) a la **intranet**

![intranet_exterior.png](https://github.com/CeliaGMqrz/servidor_Nginx/blob/main/capturas/intranet_exterior.png)


4. Comrprobamos que sí podemos acceder desde la red local (máquina cliente) a la **intranet**.

![intranet_interior.png](https://github.com/CeliaGMqrz/servidor_Nginx/blob/main/capturas/intranet_interior.png)


5. Configuramos el servidor y el cliente para que desde la red local no se pueda acceder a **internet** pero sí desde el exterior.

Desde el **servidor**:

```sh
# Default server configuration

server {
        listen 80;

        root /srv/www/departamentos;

        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;

        server_name departamentos.iesgn.org;

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }

        location /intranet {
                allow 10.0.0.6/24;
                deny all;
        }

        location /internet {
                allow 172.23.0.0/16;
                deny all;
        }

}


```

Desde el **cliente**:

```sh
# Your system has configured 'manage_etc_hosts' as True.
# As a result, if you wish for changes to this file to persist
# then you will need to either
# a.) make changes to the master file in /etc/cloud/templates/hosts.debian.tmpl
# b.) change or remove the value of 'manage_etc_hosts' in
#     /etc/cloud/cloud.cfg or cloud-config from user-data
#
127.0.1.1 cliente-nginx.novalocal cliente-nginx
127.0.0.1 localhost

10.0.0.5 departamentos.iesgn.org
# The following lines are desirable for IPv6 capable hosts
::1 ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
ff02::3 ip6-allhosts

```


6. Comprobamos que podemos acceder desde el exterior con la red pública a 'internet' pero no desde el interior por la red local.

* Red pública:

![internet_publica.png](https://github.com/CeliaGMqrz/servidor_Nginx/blob/main/capturas/internet_publica.png)

* Red local: 

![internet_cliente.png](https://github.com/CeliaGMqrz/servidor_Nginx/blob/main/capturas/internet_cliente.png)


* **Tarea 7 (1 punto): Autentificación básica. Limita el acceso a la URL departamentos.iesgn.org/secreto. Comprueba las cabeceras de los mensajes HTTP que se intercambian entre el servidor y el cliente.**

Creamos el direcotorio secreto en departamentos y le añadimos un index.html

```sh
root@servidor-nginx:/home/debian# mkdir /srv/www/departamentos/secreto
root@servidor-nginx:/home/debian# cd /srv/www/departamentos/
root@servidor-nginx:/srv/www/departamentos# ls
index.html  internet  intranet	secreto
root@servidor-nginx:/srv/www/departamentos# cp index.html secreto/
root@servidor-nginx:/srv/www/departamentos# cd secreto/
root@servidor-nginx:/srv/www/departamentos/secreto# ls
index.html
root@servidor-nginx:/srv/www/departamentos/secreto# nano index.html 

```
Creamos el fichero .htpasswd con el paquete **apache2-utils** en el directorio /srv

```sh
htpasswd -cb .htpasswd servidor servidor1
```

Configuramos nuestro fichero de configuracion de 'departamentos' y le añadimos la opción de autentificación básica. Apuntamos al fichero /srv/.htpasswd donde tenemos el usuario (servidor) y la contraseña (servidor1) que hemos configurado previamente.

```sh
# Default server configuration

server {
        listen 80;

        root /srv/www/departamentos;

        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;

        server_name departamentos.iesgn.org;

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }

        location /intranet {
                allow 10.0.0.6/24;
                deny all;
        }

        location /internet {
                allow 172.23.0.0/16;
                deny all;
        }

        location /secreto {
                auth_basic "Zona de control";
                auth_basic_user_file /srv/.htpasswd;
        }
}

```
Ahora comprobamos que desde nuestra máquina física nos pide usuario y contraseña


![secreto1.png](https://github.com/CeliaGMqrz/servidor_Nginx/blob/main/capturas/secreto1.png)


![secreto2.png](https://github.com/CeliaGMqrz/servidor_Nginx/blob/main/capturas/secreto2.png)


Vemos las cabeceras http con el comando **curl**

```sh
celiagm@debian:~/github/servidor_Nginx$ curl -I departamentos.iesgn.org/secreto
HTTP/1.1 401 Unauthorized
Server: nginx/1.14.2
Date: Wed, 11 Nov 2020 13:24:34 GMT
Content-Type: text/html
Content-Length: 195
Connection: keep-alive
WWW-Authenticate: Basic realm="Zona de control"

```
Vemos las cabeceras http con el comando **wget**

```sh
celiagm@debian:~/github/servidor_Nginx$ wget -S departamentos.iesgn.org/secreto
--2020-11-11 14:26:09--  http://departamentos.iesgn.org/secreto
Resolviendo departamentos.iesgn.org (departamentos.iesgn.org)... 172.22.200.152
Conectando con departamentos.iesgn.org (departamentos.iesgn.org)[172.22.200.152]:80... conectado.
Petición HTTP enviada, esperando respuesta... 
  HTTP/1.1 401 Unauthorized
  Server: nginx/1.14.2
  Date: Wed, 11 Nov 2020 13:26:09 GMT
  Content-Type: text/html
  Content-Length: 195
  Connection: keep-alive
  WWW-Authenticate: Basic realm="Zona de control"

La autentificación usuario/contraseña falló.

```

* **Tarea 8 (2 punto): Vamos a combinar el control de acceso (tarea 6) y la autentificación (tarea 7), y vamos a configurar el virtual host para que se comporte de la siguiente manera: el acceso a la URL departamentos.iesgn.org/secreto se hace forma directa desde la intranet, desde la red pública te pide la autentificación. Muestra el resultado al profesor.**

Para ello debemos modificar el fichero de configuración de departaentos en el servidor:

Añadimos estas dos líneas:

```sh

                satisfy any;
                allow 10.0.0.0/24;

```


De forma que con la directiva **safisfy any** se concede el acceso si un cliente cumple al menos una condición. Es decir se le permite el acceso al cliente y al resto de máquinas que estén conectadas a la red interna, pero se le pide autentificación al resto.

Quedaría tal que así:

```sh

# Default server configuration

server {
        listen 80;

        root /srv/www/departamentos;

        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;

        server_name departamentos.iesgn.org;

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }

        location /intranet {
                allow 10.0.0.6/24;
                deny all;
        }

        location /internet {
                allow 172.23.0.0/16;
                deny all;
        }

        location /secreto {
                satisfy any;
                allow 10.0.0.0/24;
                auth_basic "Zona de control";
                auth_basic_user_file /srv/.htpasswd;
        }
}

```





Vemos que que desde fuera nos pide autentificación. 

Para que se pueda probar la autentificacion el usuario es **servidor** y la contraseña es **servidor1**.


![secreto3.png](https://github.com/CeliaGMqrz/servidor_Nginx/blob/main/capturas/secreto3.png)

![secreto4.png](https://github.com/CeliaGMqrz/servidor_Nginx/blob/main/capturas/secreto4.png)


Aquí vemos que desde fuera nos pide la autentifición y desde la intranet nos deja entrar sin autentificación.

![secreto5.png](https://github.com/CeliaGMqrz/servidor_Nginx/blob/main/capturas/secreto5.png)