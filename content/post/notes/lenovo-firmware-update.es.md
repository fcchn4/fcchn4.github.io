+++
title = "Lenovo Actualización Firmware BIOS - UEFI"
author = "Fcch"
date = "2026-09-03"
description = "Un artículo breve sobre comandos para actualización de BIOS - UEFI maquinas Lenovo"
featured = false
tags = [
    "bash"
]
categories = [
    "apuntes",
]
series = ["Linux"]
thumbnail = "images/lenovo-firmware-update/lenovo-tp-cx1.png"
+++

Cuando eres usuario de algún GNU/Linux es normal toparse con varios impedimentos para actualizar o aplicar parches de seguridad en firmware BIOS, muchas empresas solo facilitan métodos de actualización con ejecutables que solo funcionan en Windows, aunque algunos métodos facilitan binarios en archivos **.iso**, **.cap** o **.cab** o actualizaciones que se pueden almacenar en dispositivos de almacenamiento USB, esta tarea no es fácil para un usuario sin mucha experiencia.

<!--more-->

![](/images/lenovo-firmware-update/lenovo-tp-cx1.png)

Algunas marcas como [Lenovo](https://www.lenovo.com/) cuentan con el famoso [LVFS (Linux Vendor Firmware Service)](https://fwupd.org/), un portal web seguro que permite a los fabricantes de hardware subir actualizaciones de firmware para equipos que utilizan sistemas operativos GNU/Linux.

Para los ejemplos que siguen utilicé una laptop Lenovo ThinkPad Carbon X1, con las siguientes características:

- Sistema Operativo: [Debian 13, Trixie](https://www.debian.org/releases/trixie/index.es.html)
- Arquitectura: [AMD64, 64 bits](https://www.debian.org/ports/amd64/)
- Entorno Gráfico: [XFCE4, versión 4.20](https://www.xfce.org/about/tour420)

El utilitario que sevamos a ejecutar es **fwupdmgr**, que viene en los repositorios oficiales de [Debian](https://www.debian.org/).

## Instalación utilitario

```bash
sudo apt install -y fwupd
```

Luego de la instalación se agregan estos dos comandos:

- **fwupdmgr**: Descarga automáticamente las actualizaciones desde LVFS.
- **fwupdtool**: Utilidad de administración más avanzada que se ejecuta como administrador para debug o instalaciones locales directas de archivos **.cap** o **.cab**

## Actualización estándar y segura (Recomendada)

```bash
fwupdmgr refresh
```

## Buscar actualizaciones disponibles

```bash
fwupdmgr get-updates
```

## Descargar e instalar las actualizaciones de firmware

```bash
sudo fwupdmgr update
```

Con estos pasos puedes tener actualizado tu firmware **BIOS/UEFI**, si necesitas ejecutar la actualización en base a archivos .cap o .cab debería ser algo similar a esto:

Obtén el ID de tu System Firmware:

```bash
fwupdmgr get-devices
```

(Busca el GUID o Device ID correspondiente a System Firmware en la salida del comando). Instala el archivo de firmware localmente:

```bash
sudo fwupdtool install-blob /ruta/del/archivo.cap <DEVICE-ID>
```

(Reemplaza **/ruta/del/archivo.cap** con la ubicación de tu archivo y **<DEVICE-ID>** con el ID de tu firmware).

Recuerda que en algunos casos para aplicar cambios es necesario ser sudo para que el comando funcione correctamente.

## Alternativas visuales

Cuando era usuario de Gnome, las actualizaciones se ejecutaban de forma automática; la herramienta que se usaba era **gnome-firmware**. Con XFCE4 prefiero ejecutarlas desde la terminal cuando sea necesario.

## Referencias

- [Linux Vendor Firmware Service](https://fwupd.org/).
