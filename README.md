# Guía-ICINGA2.
## Guía de instalación de Icinga2.
## Instalación en Ubuntu Linux:

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
