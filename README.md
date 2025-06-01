![Portada](pictures/portada2.png)
#Hola
## 🛠️ Proyecto de Monitoreo con Zabbix + Base de Datos en Contenedor

Este proyecto propone una solución de monitoreo moderna basada en Zabbix, con enfoque en el despliegue y supervisión de una base de datos MariaDB ejecutada dentro de un contenedor Docker. Su diseño modular permite escalar y replicar fácilmente en entornos de desarrollo, pruebas o producción.

## 🎯 Objetivos Principales

- 📦 Desplegar una base de datos (MariaDB) en un contenedor Docker.
- 🔍 Implementar un entorno completo de monitoreo:
  - Zabbix Server
  - Zabbix Agent
  - Zabbix Frontend
- 🛠️ Configurar plantillas y elementos de monitoreo personalizados para MariaDB.
- 📚 Proveer documentación clara para facilitar la instalación, configuración y escalabilidad.

## 🔧 Tecnologías Utilizadas

- Docker
- MariaDB
- Zabbix
- Dockerfile / Docker Compose
- Bash / Python para automatización
- Sistema operativo base: Ubuntu Server

## 🚀 Primeros Pasos

### 1. Instalar Docker en el servidor

Asegúrate de tener Docker instalado antes de continuar. Este proyecto fue probado sobre Ubuntu Server.

### 2. Preparar la imagen de MariaDB

⚠️ Importante: Zabbix requiere una versión específica de MariaDB. Usar una versión incompatible puede causar errores durante la instalación.

Usaremos:

docker pull mariadb:11.4.5

### 3. Ejecutar contenedor de MariaDB

📌 Se utilizará almacenamiento persistente en `/opt/zabbix/per_data` y se expondrá el puerto `3308` hacia el host.

docker run --restart=always -d \\
  --name zabbix-db \\
  -e MYSQL_ROOT_PASSWORD=password9 \\
  -e MYSQL_DATABASE=zabbix \\
  -p 3308:3306 \\
  -v /opt/zabbix/per_data:/var/lib/mysql \\
  mariadb:11.4.5

🔐 Reemplaza `password9` por una contraseña segura para producción.

### 4. Verificar conexión a la base de datos

Puedes probar la conexión desde el host:

mysql --host 127.0.0.1 -P3308 -u root -p

## ⚙️ Configurar Zabbix

Instalaremos Zabbix según la documentación oficial, utilizando la base de datos previamente desplegada.

Si es necesario, otorga permisos adicionales en la base de datos:

GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'%' IDENTIFIED BY 'password';
FLUSH PRIVILEGES;

🔎 '%' permite conexiones desde cualquier IP. Para mayor seguridad, usa solo la IP específica del contenedor o la red Docker.

## 🌐 Consideraciones de Red y Seguridad

La red por defecto de Docker usa el rango 172.17.0.0/16. Si estás accediendo desde otro contenedor, la IP visible para MySQL será probablemente 172.17.0.1.

Crea el usuario con permisos específicos según la IP desde la que se conectará Zabbix:

CREATE USER 'zabbix'@'172.17.0.1' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'172.17.0.1';

## 🧱 Inicializar la Base de Datos de Zabbix

Una vez creados los permisos y base de datos:

CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
SET GLOBAL log_bin_trust_function_creators = 1;

Cargar la estructura inicial de la base de datos:

zcat /usr/share/zabbix/sql-scripts/mysql/server.sql.gz | \\
  mysql --default-character-set=utf8mb4 --host 127.0.0.1 -u zabbix -P 3308 -p zabbix

## 🛡️ Notas de Seguridad

- Nunca expongas la base de datos a redes públicas sin restricciones de acceso.
- Al usar 'zabbix'@'%', cualquier IP puede conectarse si conoce la contraseña.
- Considera el uso de redes personalizadas en Docker o reglas de firewall para restringir el acceso.

## 🧪 Mejoras: Usar Dockerfile o Docker Compose

### 🏗️ Dockerfile

Puedes personalizar la configuración de MariaDB y automatizar su despliegue mediante un Dockerfile:

FROM mariadb:11.4.5
ENV MYSQL_ROOT_PASSWORD=password9

### 🧰 Docker Compose

version: '3.1'
services:
  db:
    image: mariadb:11.4.5
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: password9
      MYSQL_DATABASE: zabbix
    ports:
      - "3308:3306"
    volumes:
      - /opt/zabbix/per_data:/var/lib/mysql

## 🧩 Utilidades y Debug

Establecer configuración de idioma:

sudo locale-gen en_US.UTF-8
sudo update-locale LANG=en_US.UTF-8

Ver últimos mensajes del sistema:

tail -n 100 /var/log/syslog

## ✅ Estado Actual

- ✅ Base de datos MariaDB corriendo en contenedor  
- ✅ Usuario y permisos configurados  
- ✅ Zabbix listo para conectarse y comenzar monitoreo  

## 💬 Comentarios y Mejoras

Este proyecto está en evolución. Si tienes sugerencias o encuentras errores, ¡los issues y pull requests son bienvenidos!
