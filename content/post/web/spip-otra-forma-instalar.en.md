+++
title = "SPIP, Another Way to Install"
author = "Fcch"
date = "2020-01-06"
description = "Web Guide"
featured = true
tags = [
    "web",
    "spip",
    "cms"
]
categories = [
    "web",
    "spip",
]
series = ["Web Guide"]
thumbnail = "images/spip-svn/logo-spip-400.png"
+++
[SPIP](https://www.spip.net/) es un [CMS](https://es.wikipedia.org/wiki/Sistema_de_gesti%C3%B3n_de_contenidos) francés de instalación simple, no necesita de conocimientos de PHP y MySQL para proceder en la instalación, contiene un menú de configuración web y un espacio de administración simple.

<!--more-->

![](/images/spip-svn/logo-spip-400.png)

Existen diferentes métodos de instalación en el [sitio oficial](https://www.spip.net/es_download), se puede descargar el archivo Zip para descomprimir y preparar la instalación, otra forma que existe es iniciar la **Instalación Automática** que consiste en descargar el archivo **spip_loader.php** y ejecutarlo desde un navegador web.

El método que no se utiliza muy seguido es hacer una descarga desde un repositorio oficial de [SPIP](https://www.spip.net/) utilizando el viejo y confiable Subversion (SVN), este método de instalación es la que se utiliza comunmente para poder realizar las actualizaciones de forma automática en una sola linea de comando.

## Descarga SPIP

Iniciamos la descarga suponiendo que ya se cuenta con algún servidor web en la maquina local o con algun proveedor de su preferencia.

Para iniciar la descarga de SPIP, para tener la última versión estable 3.2.7 debemos descargar la rama [**spip-3.2**](https://www.spip.net/es_download) ejecutando:

```cmd
$ mkdir spip-core
$ cd spip-core
$ svn checkout svn://trac.rezo.net/spip/branches/spip-3.2 .
```

Luego de terminar la descarga tendremos una estructura de archivos similar a esta:

![](/images/spip-svn/spip-tree.png)

- Para las actualizaciones del CMS solo se debe ejecutar:

```cmd
$ svn upgrade
$ svn update 
```

- Para realizar las personalizaciones en SPIP sin riesgo a perder la información después de las actualizaciones se deben crear los siguientes directorios:

```cmd
$ mkdir -p plugins/auto
$ mkdir squelettes
```

- Para iniciar la instalación de SPIP necesitamos tener los directorios **IMG**, **tmp**, **local** y **config** con permisos 777 y en lo posible con usuario y grupo **www-data**:

```cmd
$ chown -R www-data:www-data IMG tmp local config
$ chmod 777 IMG tmp local config
```

La estrutura de directorios debería quedar de la siguiente forma:

![](/images/spip-svn/spip-tree-complete.png)

## Instalación SPIP

Con todos los preparativos listos podemos inicar la instalación de [**SPIP**](https://www.spip.net/).

Para iniciar la instalación de [**SPIP**](https://www.spip.net/) abrimos el navegador web de nuestra preferencia y dependiendo al caso ingresamos a la URL:

- http://localhost/ecrire
- https://dominio.com/ecrire
- http://IP-server/ecrire

esto depende de la configuración del servidor web, si esta trabajando en local o desde un proveedor del servicio.

- 1. Cuando ingresamos a la URL tenemos la bienvenida de SPIP, donde se puede elegir el idioma de instalación:

![](/images/spip-svn/spip-demo-01.png)

- 2. Luego tenemos el menu para la conexión a la base de datos, en este caso se dan dos opciones, la primera es **MySQL** donde se deben ingresar el usuario y contraseña.

![](/images/spip-svn/spip-demo-02.png)

Para el ejemplo utilizaremos la base de datos SQLite3 para una instalación rapida.

![](/images/spip-svn/spip-demo-03.png)

- 3. Ahora se crea una base de datos con nombre y prefijo [**SPIP**](https://www.spip.net/), esto en el caso de no existir bases de datos existentes.

![](/images/spip-svn/spip-demo-04.png)

- 4. Luego de crear la base de datos ahora se debe ingresar los datos para el usuario que administrará el CMS.

![](/images/spip-svn/spip-demo-05.png)

- 5. Por último se despliega la instalación listando los plugins que son parte del CMS.

![](/images/spip-svn/spip-demo-06.png)

- 6. Si todo salio bien deberiamos poder ingresar al espacio de Administración **espacio privado**.

![](/images/spip-svn/spip-demo-07.png)