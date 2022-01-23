+++
title = "VueJS + Laravel, Deploy"
author = "Fcch"
date = "2022-01-22"
description = "Servers and Infrastructure Guide"
featured = false
tags = [
    "vuejs",
    "laravel",
    "ubuntu"
]
categories = [
    "Infrastructure",
]
series = ["Servers"]
thumbnail = "images/vuejs-laravel-mix/vuejs-laravel-logo.png"
+++

At work we had to deploy a project developed with **[VueJS](https://vuejs.org)** and **[Laravel](https://laravel.com/)** on an **[Ubuntu](https://ubuntu.com/)** server, with **[Apache](https://ws.apache.org/)**, **[PHP](https://www.php.net/)**, **[MariaDB](https://mariadb.org/)**, where everything was versioned on **[github](https://github.com/)**, it had no development branches, nor fork repositories. Deploying the project had some details that we need to understand and learn so as not to make mistakes in the deployment to production.

<!--more-->

![](/images/vuejs-laravel-mix/vuejs-laravel-logo.png)

## Version Details

The versions that were used for the development and deployment of the project are:

1. [NodeJS LTS 16](https://nodejs.org/en/download/)
2. [VueJS 2](https://vuejs.org/v2/guide/installation.html)
3. [PHP 8](https://www.php.net/downloads)
4. [Laravel 8](https://laravel.com/docs/8.x)
5. [MariaDB 10.6](https://mariadb.org/download/?t=repo-config&d=20.04+%22focal%22&v=10.6&r_m=cnrs)
6. [Apache 2](https://ubuntu.com/tutorials/install-and-configure-apache#1-overview)
7. [Ubuntu Server 20.04](https://releases.ubuntu.com/20.04/)
8. [Composer 2.2.5](https://github.com/composer/composer/releases/download/2.2.5/composer.phar)

The necessary packages were installed from the official repositories of each tool and also official packages of the operating system, for **PHP8** we used [**PPA**](https://help.ubuntu.com/stable/ubuntu-help/addremove-ppa.html.es) repositories, this due to the urgency of the case.

## Installation and Configuration

The tasks we perform were automated with [**Ansible 2.9**](https://docs.ansible.com/ansible/latest/roadmap/ROADMAP_2_9.html), among common tasks we can give a simple detail.

- Required packages for the operating system:

```bash
$ sudo apt install software-properties-common dirmngr apt-transport-https git curl vim make
```

- We add the MariaDB 10.6 repository:

```bash
$ sudo apt-key adv --fetch-keys 'https://mariadb.org/mariadb_release_signing_key.asc'
$ sudo add-apt-repository 'deb [arch=amd64,arm64,ppc64el,s390x] https://mirror1.cl.netactuate.com/mariadb/repo/10.6/ubuntu focal main'
```

- We add the PPA repository for PHP8:

```bash
$ sudo add-apt-repository ppa:ondrej/php
```

- In our tests we also installed NodeJS 16, but for Ubuntu Server it is not necessary:

```bash
$ curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash - 
```

- With all the repositories added we start the update of the operating system:

```bash
$ sudo apt update && sudo apt upgrade -y
```

- Install the packages for each service:

```bash
$ sudo apt install -y rsync mariadb-client mariadb-server apache2 apache2-utils
```

- For PHP8 we specify the version:

```bash
$ sudo apt install -y php8.0 php8.0-common mcrypt php8.0-gd php8.0-cli php8.0-curl php8.0-mysql \
php8.0-zip libapache2-mod-php8.0 php8.0-odbc php8.0-pgsql php8.0-sqlite3 php8.0-readline\
php8.0-xml php8.0-intl php8.0-mbstring
```

- The installation for Composer:

```bash
$ sudo wget -O /usr/local/bin/composer https://github.com/composer/composer/releases/download/2.2.4/composer.phar
$ sudo chmod a+x /usr/local/bin/composer
```

With these important details we have the server ready to run the project, finally we will enable Apache modules and we will leave the VirtualHost detail for the project as a reference.

```bash
$ sudo a2enmod headers rewrite
```

For the Apache VirtualHost configuration:

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

To enable the new configuration:

```bash
$ sudo a2ensite vuejs-laravel.conf
$ sudo a2dissite 000-default.conf
$ sudo systemctl reload apache2.service
$ sudo systemctl restart apache2.service
```

## Project Deployment

This project is a special case, where [**VueJS**](https://vuejs.org/) does not generate a **dist** folder, in the development process all the final project is generated in the **public** folder, after creating the Laravel application it requires the installation of **laravel/ui** with **composer**, then it is executed **php artisan ui vue** to install [**VueJS**](https://vuejs.org/), with these steps you have a **mix** project between [**Laravel**](https://laravel.com/) and [**VueJS**](https://vuejs.org/).

It is normal for developers to test locally using commands like:

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

For the deployment on the production server the execution of the previous commands are no longer necessary.

Initially it is necessary to clone the repository with a name similar to the one that appears in the VirtualHost configuration file.

```bash
$ sudo -u www-data git clone git@github.com:username/vuejs-laravel.git /var/www/html/demo-web
```

Before running the necessary commands, we verify that the **modules**, **vendor** and **.env** files are not versioned, and that the **.gitignore** file has them listed.

To finish we execute the following commands:

```bash
$ cp /path/.env /var/www/html/demo-web/
$ cd /var/www/html/demo-web
$ sudo -u www-data php artisan key:generate
$ sudo -u www-data composer install
```

With this we can now test and verify the operation of our project.

## References

- [Laravel Frontend](https://laravel.com/docs/7.x/frontend)
