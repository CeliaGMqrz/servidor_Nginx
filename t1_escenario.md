# Escenario

Tarea 1 (1 punto)(Obligatorio): Crea una máquina del cloud con una red pública. Añade la clave pública del profesor a la máquina. Instala el servidor web nginx en la máquina. Modifica la página index.html que viene por defecto y accede a ella desde un navegador.

* Entrega la ip flotante de la máquina para que el profesor pueda acceder a ella.
* Entrega una captura de pantalla accediendo a ella.


______________________________________________________________________________________

#### Creación del escenario

* Creamos la instancia en el cloud y le agregamos una ip flotante.

![instancia.png]()

La ip flotante es: 172.22.200.152

* Abrimos los puertos 22 y 80. Que necesitaremos para acceder por ssh y para el servidor el http.

![reglas.png]()

* Añadimos la clave pública del profesor en el fichero authorized_keys

* Probamos el acceso

![acceso.png]()

#### Instalación de Ngix

* Para instalar Nginx vamos a usar el repositorio de Debian, así que lo primero será actualizar la información de paquetes.

```sh
sudo su
apt update
apt upgrade
```

* Instalamos el paquete nginx

```sh
apt install nginx
```

* Modificar el html y visualizar el sitio

```sh
root@servidor-nginx:/var/www/html# nano index.html 
```

![pruebahtml.png]()

[Tarea 2: Crear virtualhosting](https://github.com/CeliaGMqrz/servidor_Nginx/blob/main/t2_virtualhosting.md)