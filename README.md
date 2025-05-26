🛠 Proyecto de Monitoreo con Zabbix + Base de Datos en Contenedor
Este proyecto tiene como objetivo la creación de una solución de monitoreo basada en Zabbix, enfocada en el despliegue y supervisión de una base de datos dentro de un contenedor. Está pensado como una implementación modular y fácilmente replicable para entornos de desarrollo, pruebas o producción.

🎯 Objetivos principales
Desplegar una base de datos (MariaDB) dentro de un contenedor.

Implementar un entorno de monitoreo con Zabbix Server, Zabbix Agent y Zabbix Frontend en el servidor

Configurar plantillas y elementos de monitoreo personalizados para la base de datos.

Proveer documentación clara para replicar y escalar el entorno.

🔧 Tecnologías utilizadas
Dockerfile

Zabbix (Server, Agent, Frontend)

MariaDB

Python / scripts de automatización



Principalmente iniciaras instalando Docker en tu servidor 

trabajaremos bajo Ubuntu server

debes tener en cuenta que para zabbix es necesario utilizar una version de BD, si descargas la ultima version y esta no es la version soportada tendras problemas.

en este caso utilizaremos el pull

docker pull mariadb:11.4.5

utilizaremos la ejecucion del contenedor de la siguiente manera:

tenga en cuenta que para este documento utilizaremos como contrase;a password9 usted debe poner un password mas seguro y de ambiente de produccion 

Nota: tenga en cuenta que estamos utilizando comando de cmd y que estos quedaran, puede utilizar algunos de los metodos para eliminar o no permitir que esta infomracion se guarde en su cmd.

utilizaremos almacenamiento persistente y crearemos la data en opt, existen mas metodos para almacenar la infomracion y asegurar la data, pero este es el metodo que utilziaremos en esta entrega.

exponemos el puerto 3308 hacia el exterior

docker run --restart=always -d --name zabbix -e MYSQL_ROOT_PASSWORD=password9 -e MYSQL_DATABASE=zabbix -p 3308:3306 -v /opt/zabbix/per_data:/var/lib/mysql mariadb:11.4.5

nuestro contenedor esta corriendo y probaremos que nos podamos conectar a la base de datos

hacemos pruebas de conexion interna y externa podemor ver como esta base de datos es accesible

