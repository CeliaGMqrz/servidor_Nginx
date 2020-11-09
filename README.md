# Servidor Web Nginx

**NGINX** es un servidor web open source de alta performance que ofrece el contenido estático de un sitio web de forma rápida y fácil de configurar. Ofrece recursos de equilibrio de carga, proxy inverso y streaming, además de gestionar miles de conexiones simultáneas. El resultado de sus aportes es una mayor velocidad y escalabilidad.

Además de otras tareas, los servidores web son los encargados de la entrega de aplicaciones web, respondiendo a peticiones HTTPS realizadas por usuarios, normalmente desde un navegador web.

El funcionamiento base de Nginx es similar al de otros servidores web, en el que un usuario realiza una petición a través del navegador al servidor, y este le envía la información solicitada al navegador.

Lo que hace diferente a Nginx es su arquitectura a la hora de manejar procesos, ya que otros servidores web como Apache crean un hilo por cada solicitud.


## **Nginx VS Apache**

La principal diferencia entre Nginx y Apache está en su arquitectura. Como comentamos anteriormente Nginx puede manejar múltiples solicitudes en un solo hilo mientras que apache creará un hilo para cada solicitud.

De todas maneras, no todo son ventajas en el uso de nginx frente Apache. Nginx por ejemplo no admite el archivo .htaccess para configuraciones de la aplicación web. Sería necesario hacer estas modificaciones a nivel de servidor.

Esto implica que a la hora de usar aplicaciones web (cms) sea necesario realizar configuraciones adicionales en el servidor para su correcto funcionamiento.

[Más informacion](https://rockcontent.com/es/blog/nginx/)

Tareas para la práctica:

* Tarea 1: [Escenario]()

### Virtualhosting

* Tarea 2: [Virtual Hosting]()

### Mapeo de URL

* Tarea 3, 4 y 5: [Mapeo de URL]()

### Autentificación, Autorización y Control de Acceso

* Tarea 6, 7 y 8: [Autentificación, Autorización y Control de Acceso]()