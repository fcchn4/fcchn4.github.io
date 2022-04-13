+++
title = "Apuntes XFCE4"
author = "Fcch"
date = "2022-04-13"
description = "Apuntes para configuraciones XFCE4"
featured = false
tags = [
    "xfce",
]
categories = [
    "Apuntes",
]
series = ["GNU-Linux"]
thumbnail = "images/notes-xfce/xfce-hero.jpg"
+++

Estuve cambiando de hardware en mi ambiente de trabajo, cambie el teclado a uno en idioma inglés, por este motivo estuve configurando [**XFCE**](https://xfce.org/), que es mi entorno gráfico preferido.

<!--more-->

![](/images/notes-xfce/xfce-hero.jpg)

## Cambio de Idioma del Teclado en XFCE4

Lo primero fue hacer el cambio de idioma, por lo tanto para llegar al menú de configuración: **Settings** > **Keyboard** > **Layout**, la configuración deberia quedar con en la imagen.

![](/images/notes-xfce/xfce-keyboard.png)

Esta configuración no tuvo efecto luego de un reinicio, para solucionar este problema editamos el archivo de configuración general en: **/etc/default/keyboard**.

![](/images/notes-xfce/xfce-general.png)

La configuración debería quedar similar a la imagen.

Teclado abreviado para caracteres especiales:

- Alt Gr + ' + (a, e, i, o, u) = á, é, í, ó, ú **✓**
- Alt Gr + Shift + ~ + n = ñ **✓**

## Activar Tecla Super + Whisker Menu

Para activar el menú de aplicaciones en XFCE se necesita el complemento **whiskermenu**, para poder asociarlo a la tecla Super de la Izquierda (Logo Windows).

La configuración se debe llevar en: **Settings** > **Keyboard** > **Application Shortcuts**.

![](/images/notes-xfce/xfce-menu.png)

```bash
> xfce4-popup-whiskermenu
```

## Conexión VPN

Por último, la instalación de XFCE para Debian Bullseye o Ubuntu Focal no viene instalado por defecto los utilitarios para OpenVPN, entonces es necesario instalar los paquetes necesarios.

```bash
$ sudo apt install -y openvpn network-manager-openvpn network-manager-openvpn-gnome
```

![](/images/notes-xfce/xfce-vpn.png)

## Referencias

- [**XFCE**](https://xfce.org/)
