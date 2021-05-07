+++
title = "NPM EACCES"
author = "Fcch"
date = "2020-08-12"
description = "Guía Servidores e Infraestructura"
featured = true
tags = [
    "nodejs",
    "npm",
    "bash"
]
categories = [
    "Infraestructura",
    "Servidores",
]
series = ["Guía Servidores"]
thumbnail = "images/npm-eacces/npm-error-img.png"
+++

Me toco probar el despliegue de aplicaciones **NodeJS** donde necesitaba instalar paquetes globales, instalar paquetes **NodeJS** globales en el sistema operativo no parece ser una buena práctica, entonces encontramos una solución a este problema.

<!--more-->

![](/images/npm-eacces/npm-error-img.png)

Al ejecutar el comando:

```bash
    $ npm install -g <NOMBRE_PAQUETE>
```

Se presento un problema de permisos **npm ERR! code EACCES** y para solucionar el problema de forma inmediata era ejecutarlo con **sudo**, esta práctica es común pero no es lo que buscamos.

Buscando encotré que la solución para evitar el comando sudo es cambiar manualmente el directorio predeterminado de [**NPM**](https://nodejs.org/en/).

## Creación del Nuevo Directorio

Creamos una carpeta nueva donde se instalarán los nuevos programas:

```bash
    $ mkdir ~/.npm-global
```

## Configure NPM para la Nueva Ruta del Directorio

Con el comando [**npm**](https://nodejs.org/en/) cambiamos la ruta del directorio de instalación de paquetes:

```bash
    $ npm config set prefix '~/.npm-global'
```

## Variable de Entorno

En algunos casos, hay guías que te sugieren crear un archivo **.profile** para crear la variable, en este ejemplo agregamos la variable al archivo **.bashrc**:

```bash
    $ echo 'NPM_CONFIG_PREFIX=~/.npm-global' >> ~/.bashrc
```

## Actualizamos Variables

Actualizamos las variables con el siguiente comando:

```bash
    $ source ~/.bashrc
```

## Probamos la Instalación sin sudo

Para probar la nueva funcionalidad ejecutamos el mismo comando sin anteponer **sudo**:

```bash
    $ npm install -g <NOMBRE_PAQUETE>
```