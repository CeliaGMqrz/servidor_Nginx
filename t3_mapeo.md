# Mapeo de URL


Cambia la configuración del sitio web www.iesgn.org para que se comporte de la siguiente forma:

* Tarea 3 (1 punto)(Obligatorio): Cuando se entre a la dirección www.iesgn.org se redireccionará automáticamente a www.iesgn.org/principal, donde se mostrará el mensaje de bienvenida. En el directorio principal no se permite ver la lista de los ficheros, no se permite que se siga los enlaces simbólicos y no se permite negociación de contenido. Muestra al profesor el funcionamiento.

Para redireccionar *www.iesgn.org* a *www.iesgn.org/principal* necesitamos editar el fichero de configuracion de *iesgn*

```sh

# Default server configuration
#
server {
        listen 80;

        root /srv/www/iesgn;

        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;

        server_name www.iesgn.org;

        location = / {
                return 301 $scheme://www.iesgn.org/principal;
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;   
        }

        location /principal/ {
                # No se permite ver la lista de ficheros
                autoindex off;
                # No se permite el seguimiento de enlaces simbólicos
                disable_symlinks off;
                # No se permite la negociación de contenido
                try_files $uri $uri/ index.html;
        }

}

```
No nos olvidemos de crear el directorio *principal* y mover el *index.html* a su interior.

```sh
mkdir /srv/www/iesgn/principal
mv /srv/www/iesgn/index.html /srv/www/iesgn/principal/
```


______________________________________________________________________________________

* Tarea 4 (1 punto)(Obligatorio): Si accedes a la página www.iesgn.org/principal/documentos se visualizarán los documentos que hay en /srv/doc. Por lo tanto se permitirá el listado de fichero y el seguimiento de enlaces simbólicos siempre que sean a ficheros o directorios cuyo dueño sea el usuario. Muestra al profesor el funcionamiento.

* Creamos los directorios /srv/doc y /srv/www/iesgn/principal/documentos

```sh
mkdir /srv/doc
mkdir /srv/www/iesgn/principal/documentos
```

* Dentro de /srv/doc creamos los ficheros de prueba y le ponemos los permisos y propietarios adecuados para cada uno, de forma que uno sea de root y el otro sea del usuario debian

```sh
root@servidor-nginx:/srv/doc# ls -l
total 8
-rw-r--r-- 1 root   root   32 Nov 10 13:04 ficheroderoot.txt
-rw-r--r-- 1 debian debian 38 Nov 10 13:07 ficherodeusuario.txt

```

* Creamos el enlace simbólico del contendio que hay en doc hacia la carpeta documentos

```sh
root@servidor-nginx:~# ln -svf /srv/doc /srv/www/iesgn/principal/documentos
'/srv/www/iesgn/principal/documentos/doc' -> '/srv/doc'

```

**Fichero de configuración**

Usamos 'alias' para poder mostrar los documentos de la ruta /srv/doc, activamos el listado de ficheros, el seguimiento de enlaces simbólicos y le ponemos la opción *if_not_owner* para que deniegue el acceso a un archivo si no es el propietario

```sh
# Default server configuration
#
server {
        listen 80;

        root /srv/www/iesgn;

        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;

        server_name www.iesgn.org;

        location = / {
                return 301 $scheme://www.iesgn.org/principal;
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }

        location /principal {
                # No se permite ver la lista de ficheros
                autoindex off;
                # No se permite el seguimiento de enlaces simbólicos
                disable_symlinks off;
                # No se permite la negociación de contenido
                try_files $uri $uri/ index.html;
        }

        location /principal/documentos {

                #alias
                alias /srv/doc;
                # Permitir listado de ficheros
                autoindex on;
                # Permitir el seguimiento de enlaces simbólicos solo al propietario
                disable_symlinks if_not_owner;
        }

}


```
______

![documentos.png](https://github.com/CeliaGMqrz/servidor_Nginx/blob/main/capturas/documentos.png)
_________________
______________________________________________________________

* Tarea 5 (1 punto): En todo el host virtual se debe redefinir los mensajes de error de objeto no encontrado y no permitido. Para el ello se crearan dos ficheros html dentro del directorio error. Entrega las modificaciones necesarias en la configuración y una comprobación del buen funcionamiento.

Creamos los dos ficheros html dentro de un directorio *error* en nuestro virtualhosting. Y modificamos el fichero de nuestro virtualhosting

```sh
root@servidor-nginx:/etc/nginx# tree /srv/www/iesgn/error/
/srv/www/iesgn/error/
├── 403.html
└── 404.html
```

```sh
# Default server configuration
#
server {
        listen 80;

        #set $root_path /var/www/iesgn;
        #root $root_path;
        root /srv/www/iesgn;

        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;

        server_name www.iesgn.org;
        error_page 404 = /error/404.html;
        error_page 403 = /error/403.html;
        location = / {
                return 301 $scheme://www.iesgn.org/principal;
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                #try_files $uri $uri/ =404;

        }

        location /principal {
                # No se permite ver la lista de ficheros
                autoindex off;
                # No se permite el seguimiento de enlaces simbólicos
                disable_symlinks off;
                # No se permite la negociación de contenido
                try_files $uri $uri/ index.html;
        }

        location /principal/documentos {

                #alias
                alias /srv/doc;
                # Permitir listado de ficheros
                autoindex on;
                # Permitir el seguimiento de enlaces simbólicos solo al propietario
                disable_symlinks if_not_owner;

        }

}

```
Reiniciamos el servicio

Provocamos un error en la url y vemos el error 404

![404.png](https://github.com/CeliaGMqrz/servidor_Nginx/blob/main/capturas/404.png)


Cambiamos el 'autoindex on' por 'autoindex off' para denegar los permisos de visibilidad de ficheros y comprobamos que nos muestra la pagina de error 403 que hemos creado

![403.png](https://github.com/CeliaGMqrz/servidor_Nginx/blob/main/capturas/403.png)