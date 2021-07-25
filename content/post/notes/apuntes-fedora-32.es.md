+++
title = "Apuntes Fedora 32"
author = "Fcch"
date = "2020-05-25"
description = "Algunos apuntes para Fedora y la versión 32"
featured = false
tags = [
    "fedora",
]
categories = [
    "Infraestructura",
]
series = ["GNU-Linux"]
thumbnail = "images/fedora-32/fedora32-400.jpg"
+++

Después de actualizar mi Sistema Operativo [**Fedora 30**](https://getfedora.org/es/) a [**Fedora 32**](https://getfedora.org/es/), estuve solucionando algunos problemas con Docker CE, personalice el Prompt para mi Terminal y me cambié de [**Gnome 3.36**](https://www.gnome.org/) a [**Cinnamon 4.4**](https://es.wikipedia.org/wiki/Cinnamon).

<!--more-->

![](/images/fedora-32/fedora32-400.jpg)

## Prompt Terminal

Hace tiempo estuve buscando una forma de tener un prompt que me muestre la hora, la ubicación de directorio y datos sobre el estado en un repositorio git (ramas, cambios), el objetivo era no usar [**Oh-my-bash**](https://github.com/ohmybash/oh-my-bash), que es un conjunto de scripts para personalizar el prompt donde se puede elegir entre varias opciones de apariencia.

Buscando me encontré con [**__git_ps1**](https://fedoraproject.org/wiki/Git_quick_reference) que es un script que te permite obtener información sobre algún repositorio git, te muestra la rama en la que te encuentras actualmente, o si existe algún cambio en dicho repositorio, ya tenia un problema solucionado y solo quedo colorear el prompt obtener la hora y la ruta actual de directorio, estos cambios los agregue a mi archivo de configuración **~/.bashrc**, y quedo de esta forma: 

Cuando se tiene instalado el paquete de Git, entonces se cuenta con el script en el sistema operativo, si no se tiene instalado Git entonces: 

```cmd
# Instalción Git
$ sudo dnf install git

# Script /usr/share/git-core/contrib/completion/git-prompt.sh  
```

Para colorear el prompt y obtener los datos deseados tenemos que agregar los siguientes datos:

**Descripción de los datos:**

```text
- __git_ps1: Variable del script.
- t: Obtiene la hora, minutos y segundos.
- W: Muestra el nombre de carpeta actual.
- source: Ruta del script __git_ps1.
- export GIT_PS1_SHOWCOLORHINTS: Color para los datos git.
- export GIT_PS1_SHOWDIRTYSTATE: Muestra el estado actual del repo.
- export GIT_PS1_SHOWUNTRACKEDFILES: Mostrar archivos sin seguimiento.
- export PROMPT_COMMAND: Personalización del prompt.
- \[\033[0;31m\]: Color Rojo.
- \[\033[0;33m\]: Color Amarillo.
- \[\033[0;32m\]: Color Verde.
```

```bash
# Prompt personalizado ~/.bashrc
source /usr/share/git-core/contrib/completion/git-prompt.sh
export GIT_PS1_SHOWCOLORHINTS=true
export GIT_PS1_SHOWDIRTYSTATE=true
export GIT_PS1_SHOWUNTRACKEDFILES=true
export PROMPT_COMMAND='__git_ps1 "\[\033[01;33m\]\t\[\033[00m\] \[\033[01;31m\][\W]\[\033[00m\]" " \\\$ "'
```

## Docker CE

Luego de instalar Docker CE desde el repositorio [**oficial**](https://docs.docker.com/engine/install/fedora/) y realizar las configuraciones necesarias, resulta que los contenedores no tenían salida a internet, después de verificar el DNS interno de Docker, encontré que tenia un error con el firewall.

Para solucionar el problema agregamos la interface **docker0** en la zona de confianza del firewall.

```cmd
$ sudo firewall-cmd --permanent --zone=trusted --add-interface=docker0
$ sudo firewall-cmd --reload
```

## Cinnamon 

Antes de actualizarme a Fedora 32, el entorno gráfico que usaba era Gnome 3, al presentar problemas de rendimiento y el alto consumo de memoria opte por cambiar a Cinnamon 4.4, uno de los detalles importantes es que el controlador por defecto para la tarjeta de vídeo es **NOUVEAU** controlador de código abierto, instalamos el controlador **NVIDIA Corporation GF119 [NVS 310]** para mejorar el rendimiento en el vídeo.

**NVIDIA Driver 390xxx - repositorio RPMFusion**

```cmd
# Habilitando NVIDIA RPM Fusion
$ sudo dnf config-manager --set-enabled rpmfusion-nonfree-nvidia-driver

# Listando controladores disponibles
$ sudo dnf repository-packages rpmfusion-nonfree-nvidia-driver info

# Instalación del controlador
$ sudo dnf install xorg-x11-drv-nvidia-390xx akmod-nvidia-390xx
$ sudo dnf install xorg-x11-drv-nvidia-390xx-cuda

# Pruebas y control de rendimiento
$ sudo dnf install -y gwe
```

## Referencias

- [**Docker Fedora**](https://docs.docker.com/engine/install/fedora/)
- [**Fedora Git Reference**](https://fedoraproject.org/wiki/Git_quick_reference)