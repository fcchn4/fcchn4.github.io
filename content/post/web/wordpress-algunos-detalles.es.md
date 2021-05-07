+++
title = "Wordpress, Algunos Detalles"
author = "Fcch"
date = "2020-04-09"
description = "Guía web para la creación de un sitio web"
featured = true
tags = [
    "wordpress",
    "cms"
]
categories = [
    "web",
]
series = ["Guía Web"]
thumbnail = "images/wordpress-detalles/logo-wordpress-400.png"
+++
Estuve colaborando con algunas empresas que realizaban cambios en su sitios web, donde se utilizaba [Wordpress](https://wordpress.org/) como [CMS](https://es.wikipedia.org/wiki/Sistema_de_gesti%C3%B3n_de_contenidos) base, estos sitios web estaban siendo cambiados de servidor, actualizando a la versión mas actual o verificando actualizaciones en sus complementos, en el proceso se presentaron diferentes problemas, me toco ver la documentación oficial [Wordpress Codex](https://codex.wordpress.org/) y en Google para darles solución.

<!--more-->

![](/images/wordpress-detalles/logo-wordpress-400.png)

## Migración del CMS

Anteriormente los sitios web estaban alojados en proveedores de **hosting (hospedaje web)**, los nuevos servidores donde se encuentran actualmente alojados cuentan con servicios web realizando **reverse-proxy**  o **balanceo de carga**, para este caso fue necesario especificar algunas instancias en el archivo de configuración **wp-config.php** para que los archivos **CSS** y **JS** carguen de forma de correcta, otro problema que existió fue que la seguridad para el protocolo **HTTPS** mostraba el error de "Contenido mixto", entonces la solución de estos problemas se agrego lo siguiente:

```php
# wp-config.php
define( 'DB_COLLATE', '' );
.....
/** Solución al problema */
define('WP_HOME','https://www.dominio.com');
define('WP_SITEURL','https://www.dominio.com');

if (strpos($_SERVER['HTTP_X_FORWARDED_PROTO'], 'https') !== false)
       $_SERVER['HTTPS']='on';
.....
```

## Actualización del CMS

Como es normal los proveedores de **hosting** facilitan un servicio FTP para subir archivos, la forma de actualización también se hacia via FTP, los nuevos servidores no contaba con un servicio FTP, la primera solución que se quiso dar fue utilizando SSH (con claves publicas y privadas), pero al final se opto por realizar las actualizaciones de forma directa desde **Wordpress**.

```php
# wp-config.php
....
/** Actualizaciones directas */
define(‘FS_METHOD’,’direct’);
```

## Ocultar Información Sensible  del CMS

Otro de los detalles que existieron fue que se mostraba información sensible del CMS, como la versión que se utliza y el archivo **readme.html**, para estos casos se tuvo que modificar dos archivos **functions.php** y **.htaccess (o en su defecto el archivo de configuración del servidor web)**.

```php
# wp-content/themes/nombre_tema/functions.php
....
/** Ocultar version */
remove_action('wp_head', 'wp_generator');
add_filter('the_generator', '__return_false');
```

La configuración se puede realizar en **.htaccess** como en el archivo de configuración del servidor web.

```bash
## Para Apache
# .htaccess
....
<Files readme.html>
	Order allow,deny
	Deny from all
</Files>

## Para Nginx
# dominio.conf
location readme\.html{
	deny  all;
}
```