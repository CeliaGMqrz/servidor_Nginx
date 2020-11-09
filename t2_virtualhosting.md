# Virtual Hosting

Tarea 2 (2 punto)(Obligatorio): Configura la resolución estática en los clientes y muestra el acceso a cada una de las páginas.

______________________________________________________________________________________

Queremos que nuestro servidor web ofrezca dos sitios web, teniendo en cuenta lo siguiente:

1. Cada sitio web tendrá nombres distintos.

2. Cada sitio web compartirán la misma dirección IP y el mismo puerto (80).

Los dos sitios web tendrán las siguientes características:

* El nombre de dominio del primero será **www.iesgn.org**, su directorio base será **/srv/www/iesgn** y contendrá una página llamada index.html, donde sólo se verá una bienvenida a la página del Instituto Gonzalo Nazareno.

* En el segundo sitio vamos a crear una página donde se pondrán noticias por parte de los departamento, el nombre de este sitio será **departamentos.iesgn.org**, y su directorio base será **/srv/www/departamentos**. En este sitio sólo tendremos una página inicial index.html, dando la bienvenida a la página de los departamentos del instituto.

______________________________________________________________________________________

## Crear los virtual hosting

Crear el virtualhosting **iesgn** y el de **departamentos**. Para ello copiamos el archivo *default* que se encuentra en el fichero */etc/nginx/sites-available* y le ponemos el nombre de cada virtualhost.

```sh
root@servidor-nginx:/etc/nginx/sites-available# cp default iesgn
root@servidor-nginx:/etc/nginx/sites-available# ls
default  iesgn
root@servidor-nginx:/etc/nginx/sites-available# cp default departamentos
root@servidor-nginx:/etc/nginx/sites-available# ls
default  departamentos	iesgn

```

Deberemos de modificar los ficheros de forma que, eliminamos el 'default_server' para que no haya conflictos con el fichero 'default' que viene por defecto. Cambiamos la ruta de directorios indicando /srv/www/directorio. Cambiamos el 'server_name' indicando el nombre con el que vamos a acceder a nuestro sitio web.

## **IESGN**

```sh
nano /etc/nginx/sites-available/iesgn 
```

```sh
# Default server configuration
#
server {
        listen 80;

        root /srv/www/iesgn;

        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;

        server_name www.iesgn.org;

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }

}

```

## **DEPARTAMENTOS**

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

}

```

Creamos los enlaces simbólicos para la carpeta 'sites-enabled' y así activar nuestros virtualhosting, en apache utilizabamos una herramienta pero aquí hay que hacerlo manual.

```sh
ln -s /etc/nginx/sites-available/iesgn /etc/nginx/sites-enabled/iesgn
ln -s /etc/nginx/sites-available/departamentos /etc/nginx/sites-enabled/departamentos

```
Comprobamos que está bien configurado con **nginx -t**

```sh
root@servidor-nginx:/etc/nginx/sites-available# nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful

```

Crear los **directorios** correspondientes, no es necesario dar permisos ya que con Nginx usa los permisos de superusuario 'root'. Hemos creado la siguiente estructura tal y como se pide en el ejercicio:

```sh
root@servidor-nginx:/srv# tree
.
└── www
    ├── departamentos
    │   └── index.html
    └── iesgn
        └── index.html

3 directories, 2 files


```

#### Configurar el cliente (máquina anfitriona)

Configuramos nuestro fichero /etc/hosts añadiendo estas líneas

```sh
172.22.200.152 www.iesgn.org
172.22.200.152 departamentos.iesgn.org

```

Reiniciamos el servicio

```sh
systemctl restart nginx
```

Comprobamos que podemos acceder a ellas

**www.iesgn.org**

![iesgn.png](https://github.com/CeliaGMqrz/servidor_Nginx/blob/main/capturas/iesgn.png)

**departamentos.iesgn.org**

![departamentos.png](https://github.com/CeliaGMqrz/servidor_Nginx/blob/main/capturas/departamentos.png)