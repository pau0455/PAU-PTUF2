# NextCloud

## Introducción a Vagrant

**Vagrant** es un programa de virtualización que extiende otras herramientas como:**VirtualBox**, **libvirt** y **Docker**

## preparar la máquina con vagrant para instalar NextCloud

vagrant utiliza un directorio diferente para cada proyecto, para empezar crearemos ese directorio, a este lo pedemos llamar como queramos, pero para este caso lo llamaremos nextcloud.

```console
kdir nextcloud
cd nextcloud/
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

## Instalación de NextCloud

### Descargar NextCloud

Descargar el fichero de **nextcloud** desde su página official: https://nextcloud.com/es/

y este fichero lo movemos a `/var/www/html`

```console
mv lastest.zip /var/www/html
```
Y lo extraemos

```console
unzip lastest.zip
``` 
Y por último movemos todo el contenido de la carpeta **nextcloud** creada a `/var/www/html`

### Instalar NextCloud

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

4. Instalamos las librerias necesarias de php.
```console
apt install -y php libapache2-mod-php
apt install -y php-fpm php-common php-mbstring php-xmlrpc php-soap php-gd php-xml php-intl php-mysql php-cli php-ldap php-zip php-curl
```

5. Reiniciamos **apache2**.
```console
systemctl restart apache2
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

```
systemctl restart mysql
```

## Otorgar permisos
Dentro de directorio `/var/www/html`, aplicamos los siguientes comandos:

```console
cd /var/www/html
chmod -R 775 .
chown -R root:www-data .
```
