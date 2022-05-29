# Guía-ICINGA2.
## Guía de instalación de Icinga2.
### Instalación en Ubuntu Linux:

Utilice apt-get para instalar los paquetes necesarios.
```bash
apt-get update
apt-get install apt-transport-https wget gnupg
```
Descargue e instale la clave del repositorio Icinga2.

```bash
wget -O - https://packages.icinga.com/icinga.key | apt-key add -
```
Utilice el siguiente comando para descubrir su nombre de código Ubuntu linux.
```bash
. /etc/os-release; if [ ! -z ${UBUNTU_CODENAME+x} ]; then DIST="${UBUNTU_CODENAME}"; else DIST="$(lsb_release -c| awk '{print $2}')"; fi;
set | grep DIST
```
En nuestro ejemplo, el código Ubuntu linux es DISCO.

Agregue el repositorio oficial de Icinga2 a la base de datos APT.
```bash
echo "deb https://packages.icinga.com/ubuntu icinga-${DIST} main" > /etc/apt/sources.list.d/${DIST}-icinga.list
echo "deb-src https://packages.icinga.com/ubuntu icinga-${DIST} main" >> /etc/apt/sources.list.d/${DIST}-icinga.list
```
Actualice la base de datos APT e instale el paquete Icinga2.
```bash
apt-get update
apt-get install icinga2
```
Instale los plugins de monitoreo estándar del Icinga2.
```bash
apt-get install monitoring-plugins
```
Lista Icinga2 características instaled.
```bash
icinga2 feature list
Disabled features: api command compatlog debuglog elasticsearch gelf graphite influxdb livestatus opentsdb perfdata statusdata syslog
Enabled features: checker mainlog notification
```
Habilite el servicio Icinga2 para que se inicie automáticamente durante el tiempo de arranque
```bash
systemctl enable icinga2
```
Listo, ya habriamos terminado la instalación de Icinga2


### Instalación de MySQL:

Instale el paquete icinga2-ido-mysql.

Esto permitirá al servidor Icinga2 almacenar la configuración en Mysql.
```bash
apt-get update
apt-get install icinga2-ido-mysql
```
Habilite la función ido-mysql del Icinga 2.
IMAGEN
¿Configurar la base de datos para icinga2-ido-mysql con dbconfig-common?
IMAGEN

Habilite la característica ido-mysql.

```bash
icinga2 feature enable command  ido-mysql
```
Reiniciar Icinga2
```bash
service icinga2 restart
```
Instale el servicio de base de datos MySQL.

```bash
apt-get install mysql-server mysql-client
```
Acceda al servidor de bases de datos MySQL.

```bash
mysql -u root -p
```
Establezca una contraseña para el usuario raíz de MySQL.

```bash
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'onmula';
```
En nuestro ejemplo, establecemos la contraseña raíz "onmula".
Cree una base de datos denominada icinga2.

```bash
CREATE DATABASE icinga2 CHARACTER SET UTF8 COLLATE UTF8_BIN;
```
Cree un usuario mysql denominado icinga2.
```bash
CREATE USER 'icinga2'@'%' IDENTIFIED BY 'onmula';
```
Conceda al usuario de MySQL permiso denominado icinga2 sobre la base de datos denominada icinga2.
```bash
GRANT ALL PRIVILEGES ON icinga2.* TO 'icinga2'@'%';
quit;
```
Importe la plantilla de base de datos Icinga2 dentro de MySQL.
El sistema solicitará la contraseña del usuario de icinga2 MysQL para importar la plantilla.
```bash
mysql -u icinga2 -p icinga2 < /usr/share/icinga2-ido-mysql/schema/mysql.sql
```
Edite el archivo de configuración ido-mysql.conf para habilitar la comunicación con el servicio MySQL.
```bash
sudo nano /etc/icinga2/features-enabled/ido-mysql.conf
```
Aquí está nuestra configuración.
```bash
/**
 * The db_ido_mysql library implements IDO functionality
 * for MySQL.
 */

library "db_ido_mysql"

object IdoMysqlConnection "ido-mysql" {
  user = "icinga2",
  password = "kamisama123",
  host = "localhost",
  database = "icinga2"
}
```
Reiniciar Icinga2
```bash
service icinga2 restart
````
Ya dariamos por concluida la instalación de la base de datos.
Ha importado las plantillas de base de datos Icinga2 en MySQL Server.

### Instalación de la interfaz web de icinga2:

A continuación, necesitamos instalar el servidor web Apache y todo el software necesario.

En la consola Linux, utilice los siguientes comandos para instalar los paquetes necesarios.

```bash
apt-get -y install apache2 php libapache2-mod-php php-cli php-opcache php-gd
apt-get -y install php-mysql php-mbstring php-xml php-gd php-json php-curl
apt-get -y install php-bcmath php-ldap php-intl php-readline 
```
Localice el archivo de configuración PHP en su sistema.

Edite el archivo de configuración PHP y establezca la zona horaria correcta.

```bash
updatedb
locate php.ini
nano /etc/php/7.2/apache2/php.ini
```
Tenga en cuenta que su versión PHP y la ubicación del archivo pueden no ser la misma mía.

Como ejemplo, nuestra zona horaria se estableció en América/Sao_Paulo.

```bash
date.timezone = America/Sao_Paulo
```
Reinicie el servicio Apache.
```bash
service apache2 restart
```
Acceda al servidor de bases de datos MySQL.
```bash
mysql -u root -p
```
Cree una base de datos denominada icingaweb_db.
```bash
CREATE DATABASE icingaweb_db CHARACTER SET UTF8 COLLATE UTF8_BIN;
```
Cree un usuario mysql denominado icingaweb_db.
```bash
CREATE USER 'icingaweb_db'@'%' IDENTIFIED BY 'onmula';
```
Conceda al usuario MySQL el nombre icingaweb_db permiso sobre la base de datos denominada icingaweb_db.

```bash
GRANT ALL PRIVILEGES ON icingaweb_db.* TO 'icingaweb_db'@'%';
quit;
```
Instale el paquete de interfaz web de Icinga denominado icingaweb2.
```bash
apt-get install icingaweb2
```
Reinicie el servicio Apache.

```bash
service apache2 restart
```
Genere el token de instalación de Icinga.
```bash
icingacli setup token create
The newly generated setup token is: 27df50a1fffc3aa5
```
En caso de luego no recordar o perder el token generado, podemos usar el siguiente comadno: 
```bash
icingacli setup token show
```

Abra su navegador e introduzca la dirección IP de su servidor web más /icingaweb2.

En nuestro ejemplo, se introdujo la siguiente URL en el navegador:

http://10.0.2.15/icingaweb2/setup

Se debe presentar la interfaz de instalación web de Icinga2.

Ingrese el token de configuración de Icinga web2.
![1](https://user-images.githubusercontent.com/90779690/170890276-428bcba4-e789-46d3-9868-a83fc6aeba72.PNG)

Una vez introducimos el Token, continuemos a la configuracion de icinga web 2.
![2](https://user-images.githubusercontent.com/90779690/170890279-3092c117-6e25-45bc-8315-5f729bd47ed3.PNG)

Activamos la opción que dice "Monitoring", que se refiere al modulo de monitorización.
![3](https://user-images.githubusercontent.com/90779690/170890282-dfb5bfa0-f921-4183-8777-5edbc9ecac91.PNG)

Verifiamos que tengamos activas y funcionando los apartados de "Versión de PHP", "Linux Plataforma" y "Módulo de PHP:OpenSSL"
![4](https://user-images.githubusercontent.com/90779690/170890285-0aeb2bd8-2328-47bf-acdc-5a58a7693e1e.PNG)

En tipo de autenticación lo dejaremos por defecto en Database o Base de datos.

En la pantalla Recurso de base de datos de Icinga, realice la siguiente configuración:

• Nombre del recurso - icingaweb_db
• Tipo de base de datos - MYSQL
• Anfitrión - localhost
• Puerto - 3306
• Nombre de la base de datos - icingaweb_db
• Nombre de usuario - icingaweb_db
• Contraseña - onmula

Haga clic en el botón Siguiente.



![5](https://user-images.githubusercontent.com/90779690/170890287-8fa402c8-29d3-4e56-bc9a-2016d8c02236.PNG)

En esta pantalla, ingrese el inicio de sesión raíz de MySQL para importar la plantilla de base de datos Icingaweb2.
![6](https://user-images.githubusercontent.com/90779690/170890288-d4ca74cf-8133-4652-97c6-e019244748ec.PNG)

Establezca el nombre de back-end icingaweb2 y haga clic en el botón Siguiente.
![7](https://user-images.githubusercontent.com/90779690/170890292-0ed50ed5-d970-49f3-a3d5-156ff2640916.PNG)

Establezca una cuenta administrativa para acceder a la interfaz web de Icinga.
![8](https://user-images.githubusercontent.com/90779690/170890297-84916e0c-6830-46e5-bd01-ffbf58726e78.PNG)

Pantalla de configuración de la aplicación, realice la configuración de seguimiento:

• Mostrar Stacktraces - Habilitado
• Mostrar mensajes de estado de la aplicación - Habilitado
• Tipo de almacenamiento de preferencias de usuario - Base de datos
• Tipo de registro - Syslog
• Nivel de registro - Error
• Prefijo de aplicación - icingaweb2
• Facilidad - Usuario

Haga clic en el botón Siguiente.
![9](https://user-images.githubusercontent.com/90779690/170890299-3eb9b9f5-cebc-4d25-9bc8-b2f39b98f43c.PNG)

En la pantalla de resumen de instalación de Icinga, haga clic en el botón Siguiente.

En la pantalla de bienvenida, haga clic en el botón Siguiente.
![10](https://user-images.githubusercontent.com/90779690/170890302-2b3deda7-a6ce-4263-af8e-f54907c38089.PNG)

Ahora nos llevara a la pantalla del back-end de supervisión, realice la siguiente configuración:

• Nombre backend - icinga
• Tipo de backend - IDO

Luego en la pantalla Monitoring IDO Resource (Supervisión de recursos de IDO), realice la siguiente configuración:
• Nombre del recurso - icinga_ido

• Tipo de base de datos - MYSQL

• Anfitrión - localhost

• Puerto - 3306

• Nombre de la base de datos - icinga2

• Nombre de usuario - icinga2

• Contraseña - onmula

Haga clic en el botón Siguiente.


En la pantalla Transporte de comandos, realice la siguiente configuración:

• Nombre del transporte - icinga2
• Tipo de transporte - Archivo de comandación local
• Archivo De comunicación - /var/run/icinga2/cmd/icinga2.cmd

Haga clic en el botón Siguiente.


En la pantalla Seguridad de supervisión, realice la siguiente configuración:

• Variables personalizadas protegidas - *pw*,*pass*,community

Haga clic en el botón Siguiente.

En la última pantalla, haga clic en el botón Finalizar y espere a que finalice la instalación de Icinga2.


Haga clic en el botón Siguiente.
![11](https://user-images.githubusercontent.com/90779690/170890304-734e05b0-2209-45b1-a8d8-7b6edf4a848d.PNG)

Después de finalizar la instalación haga clic en la opción: Iniciar sesión en Icinga Web 2.

Por último, se le presentará la pantalla de inicio de sesión de Icinga2.

• Iicnga2 nombre de usuario predeterminado: Usted estableció antes, eneste caso será "admin".
• Contraseña predeterminada de Icinga2: Usted estableció antes, en este caso será "onmula".
![12](https://user-images.githubusercontent.com/90779690/170890758-7017b84c-3755-4d30-95df-a8a769836831.PNG)


Se debe mostrar la interfaz de inicio de sesión de Icinga2.
![13](https://user-images.githubusercontent.com/90779690/170890800-231231b6-7324-412f-b5fa-16e4b988e079.PNG)

Existe la posibilidad que nos salten alertas en mi caso, 1 crítica, llamada "disk" la cual surge por un problema con los directorios, pero no nos afectara. Otras 2 alertas más que solo nos dirá Warning y se resuelven muy facil.
Una alerta se resolvera ejecutando:
```bash
sudo apt-het update
```
y la segunda alerta se resuelve ejecutando:
```bash
sudo apt-get install ssh
```
















