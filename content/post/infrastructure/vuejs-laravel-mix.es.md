+++
title = "VueJS + Laravel, Deploy"
author = "Fcch"
date = "2022-01-22"
description = "Guía Servidores e Infraestructura"
featured = false
tags = [
    "vuejs",
    "laravel",
    "ubuntu"
]
categories = [
    "Infraestructura",
]
series = ["Servidores"]
thumbnail = "images/vuejs-laravel-mix/vuejs-laravel-logo.png"
+++

En el trabajo nos toco desplegar un proyecto desarrollado con **[VueJS](https://vuejs.org)** y **[Laravel](https://laravel.com/)** en un servidor **[Ubuntu](https://ubuntu.com/)**, con **[Apache](https://ws.apache.org/)**, **[PHP](https://www.php.net/)**, **[MariaDB](https://mariadb.org/)**, donde todo estaba versionado en **[github](https://github.com/)**, no tenia ramas de desarrollo, ni tampoco repositorios fork. Desplegar el projecto tuvo algunos detalles que necesitamos entender y aprender para no cometer errores en el despliegue a producción.

<!--more-->

![](/images/vuejs-laravel-mix/vuejs-laravel-logo.png)

## Detalle de Versiones

Las versiones que se utilizaron para el desarrollo y despliegue del proyecto son:

1. [NodeJS LTS 16](https://nodejs.org/en/download/)
2. [VueJS 2](https://vuejs.org/v2/guide/installation.html)
3. [PHP 8](https://www.php.net/downloads)
4. [Laravel 8](https://laravel.com/docs/8.x)
5. [MariaDB 10.6](https://mariadb.org/download/?t=repo-config&d=20.04+%22focal%22&v=10.6&r_m=cnrs)
6. [Apache 2](https://ubuntu.com/tutorials/install-and-configure-apache#1-overview)
7. [Ubuntu Server 20.04](https://releases.ubuntu.com/20.04/)
8. [Composer 2.2.5](https://github.com/composer/composer/releases/download/2.2.5/composer.phar)

Los paquetes necesarios fueron instalados desde los repositorios oficiales de cada herramienta y también paquetes oficiales del sistema operativo, para **PHP8** utilizamos repositorios [**PPA**](https://help.ubuntu.com/stable/ubuntu-help/addremove-ppa.html.es), esto por las urgencias del caso.

## Instalación y Configuración

Las tareas que realizamos fueron automatizadas con [**Ansible 2.9**](https://docs.ansible.com/ansible/latest/roadmap/ROADMAP_2_9.html), entre tareas comunes podemos dar un detalle simple.

- Paquetes necesarios para el sistema operativo:

```bash
$ sudo apt install software-properties-common dirmngr apt-transport-https git curl vim make
```

- Agregamos el repositorio MariaDB 10.6:

```bash
$ sudo apt-key adv --fetch-keys 'https://mariadb.org/mariadb_release_signing_key.asc'
$ sudo add-apt-repository 'deb [arch=amd64,arm64,ppc64el,s390x] https://mirror1.cl.netactuate.com/mariadb/repo/10.6/ubuntu focal main'
```

- Agreamos el repositorio PPA para PHP8:

```bash
$ sudo add-apt-repository ppa:ondrej/php
```

- En nuestras pruebas también instalamos NodeJS 16, pero para el servidor Ubuntu Server no es necesario: 

```bash
$ curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash - 
```

- Con todos los repositorios agregados iniciamos la actualización del sistema operativo:

```bash
$ sudo apt update && sudo apt upgrade -y
```

- Instalamos los paquetes para cada servicio:

```bash
$ sudo apt install -y rsync mariadb-client mariadb-server apache2 apache2-utils
```

- Para PHP8 especificamos la versión: 

```bash
$ sudo apt install -y php8.0 php8.0-common mcrypt php8.0-gd php8.0-cli php8.0-curl php8.0-mysql \
php8.0-zip libapache2-mod-php8.0 php8.0-odbc php8.0-pgsql php8.0-sqlite3 php8.0-readline\
php8.0-xml php8.0-intl php8.0-mbstring
```

- La instalación para Composer:

```bash
$ sudo wget -O /usr/local/bin/composer https://github.com/composer/composer/releases/download/2.2.4/composer.phar
$ sudo chmod a+x /usr/local/bin/composer
```

Con estos detalles importantes tenemos listo el servidor para correr el proyecto, por último habilitaremos módulos Apache y dejaremos el detalle del VirtualHost para el proyecto como referencia.

```bash
$ sudo a2enmod headers rewrite
```

Para la configuración VirtualHost de Apache:

```bash
$ sudo vim /etc/apache2/sites-available/vuejs-laravel.conf
---
<VirtualHost *:80>
    ServerAdmin info@demo.com
    DocumentRoot /var/www/html/demo-web/public
    <Directory /var/www/html/demo-web/public>
    	RewriteEngine On
    	Options -Indexes +FollowSymLinks
    	AllowOverride All
    	Order Allow,Deny
    	Allow from all
    </Directory>

    <DirectoryMatch \.git>
    	Order allow,deny
    	Deny from all
    </DirectoryMatch>

    LogLevel warn
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log vhost_combined

    <IfModule mod_rewrite>
    	RewriteLog ${APACHE_LOG_DIR}/rewrites.log
    	RewriteLogLevel 0
    </IfModule> 
</VirtualHost>
```

Para habilitar la nueva configuración:

```bash
$ sudo a2ensite vuejs-laravel.conf
$ sudo a2dissite 000-default.conf
$ sudo systemctl reload apache2.service
$ sudo systemctl restart apache2.service
```

## Despliegue del Proyecto

Este proyecto es un caso especial, donde [**VueJS**](https://vuejs.org/) no genera una carpeta **dist**, en el proceso de desarrollo todo el proyecto final se genera en la carpeta **public**, luego de crear la aplicación Laravel reqiere de la instalación **laravel/ui** con **composer**, luego se ejecuta **php artisan ui vue** para instalar **VueJS**, con estos pasos se tiene un proyecto **mix** entre [**Laravel**](https://laravel.com/) y [**VueJS**](https://vuejs.org/).

Es normal que los desarrolladores para las pruebas en local utilicen comandos como:

- NodeJS

```bash
$ npm install && npm run dev
$ npm run production
$ npm run watch
```

- PHP

```bash
$ php artisan key:generate
$ php artisan install
$ php artisan serve
```

Para el desplieqgue en el servidor de producción la ejecución de los comandos anteriores ya no son necesarios.

Inicialmente es necesario clonar el repositorio con un nombre similar al que figura en el archivo de configuración del VirtualHost.

```bash
$ sudo -u www-data git clone git@github.com:username/vuejs-laravel.git /var/www/html/demo-web
```

Antes de ejecutar los comandos necesarios, verificamos que los archivos **modules**, **vendor** y **.env** no esten versionados, y que el archivo **.gitignore** los tenga en su lista.

Para finalizar ejecutamos los siguientes comandos:

```bash
$ cp /path/.env /var/www/html/demo-web/
$ cd /var/www/html/demo-web
$ sudo -u www-data php artisan key:generate
$ sudo -u www-data composer install
```

Con esto ya podemos probar y verificar el funcionamiento de nuestro proyecto.

## Referencias

- [Laravel Frontend](https://laravel.com/docs/7.x/frontend)
