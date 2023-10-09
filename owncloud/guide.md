# OwnCloud

## Introducción a Vagrant

**Vagrant** es un programa de virtualización que extiende otras herramientas como:**VirtualBox**, **libvirt** y **Docker**.

## preparar la máquina con vagrant para instalar OwnCloud

vagrant utiliza un directorio diferente para cada proyecto, para empezar crearemos ese directorio, a este lo pedemos llamar como queramos, pero para este caso lo llamaremos owncloud.

```console
mkdir owncloud
cd owncloud/
```

Ahora con nuestra carpeta creada y dentro de del directorio realizamos el siguiente comando descrito para descargar la ISO de la máquina virtual e instalarla.

```console
vagrant init ubuntu/jammy64
```

Ahora podemos encenderlo con parámetro `--provider=*virtualbox` para asegurarnos que es virtualizado con VirtualBox usando `vagrant up` de la siguiente manera:

```console
vagrant up --provider=virtualbox
```

Si todo ha salido bien hasta este punto podemos usar el siguiente comando para conectarnos mediante SSH a la máquina virtual de vagrant:

```console
vagrant ssh
```
## Configuración de vagrant

Para que apache y, por lo tanto, apache pueda ser visible para nuestra máquina tenemos que abrir los puertos en **vagrantfile**.

descomentamos las siguientes líneas: 

`config.vm.network "forwarded_port", guest: 80, host: 8080`

`config.vm.network "public_network"`

## Instalación de OwnCloud

### Descargar OwnCloud

Descargar el fichero de **owncloud** desde su página official: https://owncloud.com/

y este fichero lo movemos a `/var/www/html`

```console
mv lastest.zip /var/www/html
```
Y lo extraemos

```console
unzip lastest.zip
``` 
Y por último movemos todo el contenido de la carpeta **owncloud** creada a `/var/www/html`

### Instalar OwnCloud

Une vez conectada mediante SSH a vagrant deberemos instalar apache, para asegurarnos de instalar la última versión haremos primero un 'apt update' y seguidamente instalamos con `apt install`apache2 y mysql.


1. Actualizamos para obtener las últimas versiones.
```console
apt update
apt upgrade
```

2. Instalamos **apache2**.
```console
apt install -y apache2
```

3. Instalamos**mysql-server**.
```console
apt install -y mysql-server
```


5. Reiniciamos **apache2**.
```console
systemctl restart apache2
```
### Añade un Repositorio PHP 

Por defecto, Ubuntu 20.04 viene con la versión 8.2. Por lo tanto, deberás agregar el repositorio de PHP en tu sistema para instalar las múltiples versiones de PHP.

Primero, instala las dependencias requeridas con el siguiente comando:
```console
apt-get install software-properties-common gnupg2 -y 
add-apt-repository ppa:ondrej/php 
apt-get update -y 
apt-get install php7.4 php7.4-fpm php7.4-cli -y
sudo apt install smbclient redis-server unzip openssl rsync imagemagick
sudo apt install php7.4 php7.4-intl php7.4-mysql php7.4-mbstring \
    php7.4-imagick php7.4-igbinary php7.4-gmp php7.4-bcmath \
    php7.4-curl php7.4-gd php7.4-zip php7.4-imap php7.4-ldap \
    php7.4-bz2 php7.4-ssh2 php7.4-common php7.4-json \
    php7.4-xml php7.4-dev php7.4-apcu php7.4-redis \
    libsmbclient-dev php-pear php-phpseclib

```

Ahora, establece como versión por defecto de PHP de la línea de comandos la versión 7.4 con el siguiente comando:

```console
update-alternatives --config php 
```

Te pedirá que establezcas la versión de PHP por defecto como se muestra más abajo:
```console
There are 3 choices for the alternative php (providing /usr/bin/php).

  Selection    Path             Priority   Status
------------------------------------------------------------
* 0            /usr/bin/php7.4   100       auto mode       manual mode
  1            /usr/bin/php7.4   74        manual mode

```

Selecciona la versión de PHP que desees y presiona "Enter" para establecerla como la versión por defecto.

A continuación, verifica la versión de PHP por defecto usando el siguiente comando:
```console
php --version
```
y por ultimo activa php en apache
```console
a2enmod php7.4
```

### Configurar MySLQ

Nos otorgamos permisos de root y ejecutamos el siguiente comando:
```console
mysql
```

Ahora en la interfaz de mysql tenemos que crear una base de datos o **bbdd**.

```console
CREATE DATABASE bbdd;
```

Establecemos un usuario, una IP y una contraseña con la que conectarnos a la base de datos, en este caso usaremos **localhost** y el usuario y contraseña lo dejaremos tal y como está en el comando explicado.

```console
CREATE USER 'usuario'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```

Otorgamos todos los privilegios a nuestro usuario:

```console
GRANT ALL ON bbdd.* to 'usuario'@'localhost';
```

Y por último salimos de la interfaz de mysql

```console
exit
```

Desde un terminal probamos a contarnos a nuestra `bbdd`.

```console

mysql -u usuario -p
```
Y si ha salido correctamente de nuevo realizamos un **exit** y reiniciamos mysql

```console
systemctl restart mysql
```

## Otorgar permisos
Dentro de directorio `/var/www/html`, aplicamos los siguientes comandos:

```console
cd /var/www/html
chmod -R 775 .
chown -R root:www-data .
```
