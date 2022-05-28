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

http://192.168.15.10/icingaweb2](http://10.0.2.15/icingaweb2/setup)

Se debe presentar la interfaz de instalación web de Icinga2.

Ingrese el token de configuración de Icinga web2.


















