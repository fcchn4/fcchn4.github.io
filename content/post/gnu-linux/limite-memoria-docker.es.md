+++
title = "Limite de Memoria en Docker"
author = "Fcch"
date = "2020-08-12"
description = "Guía Servidores e Infraestructura"
featured = true
tags = [
    "bash",
    "docker",
    "grub",
    "cgroup"
]
categories = [
    "Infraestructura",
    "Servidores",
]
series = ["Guía Servidores"]
thumbnail = "images/docker-limite-mem/kernel-panic.jpg"
+++
Cuando creamos un nuevo contenedor, el mismo tiene acceso ilimitado a los recursos del sistema, así que si uno ocupa toda la memoria los demás contenedores serán afectados o en su defecto el sistema operativo se queda sin recursos.

Docker tiene la posibilidad de aplicar un límite de memoria a un contenedor específico.

<!--more-->

![](/images/docker-limite-mem/kernel-panic.jpg)

## Creando un Contenedor

```cmd
    $ docker run --name dell -it debian bash
```

## Obteniendo Información del Contenedor

```cmd
    $ docker inspect dell | grep -i 'Memory'
```

![](/images/docker-limite-mem/docker-memory.png)

Para **"Memory": 0** el valor **0** indica que el contenedor no tiene valor limitado de memoria, es decir que utilizará todo lo que tenga disponible el host, posiblemente afectando a otros contenedores que estén dentro del mismo host.

## Actualizando la Memoria del Contenedor

```cmd
    $ docker update -m 256MB dell
```

En las pruebas existieron problemas en sistemas operativos Debian y Ubuntu:

![](/images/docker-limite-mem/docker-kernel-fail.png)

Este problema se debe a que el [**cgroup**](https://en.wikipedia.org/wiki/Cgroups) no esta montado en el sistema, para que se monte editamos el archivo archivo **grub**.

```cmd
    $ sudo vim /etc/default/grub
    ...
    GRUB_CMDLINE_LINUX_DEFAULT="cgroup_enable=memory swapaccount=1"
    GRUB_CMDLINE_LINUX="cgroup_enable=memory swapaccount=1"
    ...
```

Actualizamos el **grub** y reiniciamos el sistema:

```cmd
    $ sudo update-grub
    $ sudo update-grub2
    $ sudo reboot
```

Por último volvemos a ejecutar el comando para actualizar la memoria y verificamos el estado de la memoria:

![](/images/docker-limite-mem/docker-update-mem.png)
