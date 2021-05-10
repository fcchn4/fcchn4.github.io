+++
title = "Wordpress, Some Details"
author = "Fcch"
date = "2020-04-09"
description = "Web Guide"
featured = true
tags = [
    "wordpress",
    "cms"
]
categories = [
    "web",
]
series = ["Web"]
thumbnail = "images/wordpress-detalles/logo-wordpress-400.png"
+++
I was collaborating with some companies that made changes to their websites, where [Wordpress](https://wordpress.org/) was used as the base [CMS](https://es.wikipedia.org/wiki/Sistema_de_gesti%C3%B3n_de_contenidos), these websites were being changed servers, updating to the most current version or verifying updates in their add-ons, in the process different problems arose , I had to see the official [Wordpress Codex](https://codex.wordpress.org/) documentation and Google to give them a solution.

<!--more-->

![](/images/wordpress-detalles/logo-wordpress-400.png)

## Wordpress Migration

Previously the websites were hosted by **hosting providers (web hosting)**, the new servers where they are currently hosted have web services performing **reverse-proxy** or **load balancing**, for this In case it was necessary to specify some instances in the configuration file **wp-config.php** so that the **CSS** and **JS** files load correctly, another problem that existed was that the security for the **HTTPS** protocol showed the error of "Mixed content", then the solution of these problems was added the following:

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

## Wordpress Update

As is normal, **hosting** providers provide an FTP service to upload files, the update method was also done via FTP, the new servers did not have an FTP service, the first solution that was wanted was using SSH ( with public and private keys), but in the end it was decided to carry out the updates directly from **Wordpress**.

```php
# wp-config.php
....
/** Actualizaciones directas */
define(‘FS_METHOD’,’direct’);
```

## Hide Sensitive Wordpress Information

Another detail that existed was that sensitive CMS information was shown, such as the version used and the **readme.html** file, for these cases two files **functions.php** and **.htaccess** (or, failing that, the web server configuration file).

```php
# wp-content/themes/nombre_tema/functions.php
....
/** Ocultar version */
remove_action('wp_head', 'wp_generator');
add_filter('the_generator', '__return_false');
```

The configuration can be done in **.htaccess** as in the web server configuration file.

```cmd
## For Apache
# .htaccess
....
<Files readme.html>
	Order allow,deny
	Deny from all
</Files>

## For Nginx
# dominio.conf
location readme\.html{
	deny  all;
}
```

## References

- [Wordpress](https://wordpress.org/)
- [Wordpress Codex](https://codex.wordpress.org/)