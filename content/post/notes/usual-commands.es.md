+++
title = "Comandos Usuales Simples"
author = "Fcch"
date = "2022-05-01"
description = "Lista de comandos comunmente utilizados"
featured = true
tags = [
    "cmd",
]
categories = [
    "apuntes",
]
series = ["Linux"]
thumbnail = "images/usual-cmd/linux-cmd.jpg"
+++

Cada día aprendemos comandos que nos permiten realizar determinadas tareas en el trabajo, para no olvidar alguno de estos comandos que sirvieron cree una lista como ayuda memoria.

<!--more-->

![](/images/usual-cmd/linux-cmd-post.jpg)

## Verificar la Fecha de Instalación del SO

```bash
# Primera opción
$ ls -lct /etc | tail -1 | awk '{print $6, $7, $8}'
# Segunda opción
$ ls -lct --time-style=+"%d-%m-%Y %H:%M:%S" /etc | tail -1 | awk '{print $6, $7, $8}'
# En su defecto
$ ls -lAhF /etc/hostname
```

## Identificar el Nombre de un Servicio en Base al Puerto 

```bash
# Identificamos el ID de proceso
$ fuser -n tcp 7070
# Buscamos el nombre de proceso en base al ID
$ ps aux | grep ID_PROCESS
```

## Identificar Detalle de Hardware

```bash
$ inxi -Fxz
# Comunes: lsblk, lsusb, lsscsi, lspci, lscpu
$ hwinfo --short
$ free -m
$ cat /proc/partitions
$ sudo hdparm -i /dev/sda # Puede cambiar sda
$ ls /sys/class/net/
$ ls /sys/class/net/enp0s25 # Puede cambiar enp0s25
$ cat /sys/class/net/enp0s25/carrier
$ cat /sys/class/net/enp0s25/operstate
$ lspci -v | grep "VGA" -A 12
$ df -h
$ pydf
$ sudo fdisk -l
$ mount | column -t
$ cat /proc/cpuinfo
$ cat /proc/meminfo
$ cat /proc/scsi/scsi
$ cat /proc/version
```

## Listar Puertos Abiertos

```bash
# Con netstat
$ netstat -vatn # netstat -tlpn
# Con ss
$ ss -tlpn
```

## Para SO Debian y Derivados, Configuración Lenguaje y Zona Horaria

```bash
# vim /etc/environment
LC_ALL="es_BO.UTF-8"
# Configuración Zona Horaria
$ timedatectl set-timezone America/La_Paz
```

## Comandos DNF Probados en Fedora

```bash
# Eliminar Kernel Viejos Fedora:
$ dnf remove $(dnf repoquery --installonly --latest-limit=-2 -q)

# Eliminar Kernel Viejos CentOS:
$ package-cleanup --oldkernels --count=2

# Comando RPM:
$ rpm -qa kernel\* | sort -V

# Archivo de configuración:
## Se debe editar el archivo /etc/yum.conf para CentOS y /etc/dnf/dnf.conf 
## para Fedora y asignar installonly_limit:
$ installonly_limit=2

# Regenerar Módulos de Kernel
$ dracut --regenerate-all --force
```

## Configuraciones Gnome con Gsettings

```bash
# Gnome Shell Personalización a normal:
$ gsettings set org.gnome.shell.extensions.dash-to-dock extend-height false
$ gsettings set org.gnome.shell.extensions.dash-to-dock transparency-mode 'FIXED'
$ gsettings set org.gnome.shell.extensions.dash-to-dock background-opacity 0.0
```

## Comandos SSH

```bash
# SSH Tunneling
$ ssh -L 2228:hostname.IP_DOMINIO_PUBLICO:22 user@IP_DOMINIO_PRIVADO
$ ssh -f user@IP_DOMINIO_PRIVADA -L 2229:hostname.IP_DOMINIO_PRIVADO:22 -N
$ ssh -L 2229:hostname.IP_DOMINIO_PUBLICO:22 user@IP_DOMINIO_PRIVADO

# Ejemplos
$ ssh -i .ssh/PUB_KEY -f user@IP_DOMINIO_PUBLICO -L 2228:IP_DOMINIO_PRIVATE:22 -N
$ ssh -i .ssh/PUB_KEY user@localhost -p2228

# SSH Agent TTY
$ eval $(ssh-agent -s)
$ ssh-add ~/.ssh/name_priv_key

# Lista de usuarios con Login SSH aceptados
$ sudo cat /var/log/auth* | grep Accepted
```

## Comandos Útiles

```bash
# OpenWRT manejador de paquetes
$ opkg list-upgradable | cut -f 1 -d ' ' | xargs opkg upgrade 

# Actualizar cache de fuentes tipográficas
$ fc-cache -f -v

# Remount Linux Device
$ mount -o remount,rw -t ext4 /dev/sdb /media/device

# Poner todos los subfolder de un folder a 755:
$ find . -type d -exec chmod 755 {} \;

# Todos los archivos a 644:
$ find . -type f -exec chmod 644 {} \;

# Establecer solo los archivos que terminen con .php a 644:
$ find . -name \*\.php -exec chmod 644 {} \;
```

## SELinux y Firewall CMD

```bash
$ journalctl -t setroubleshoot
$ dmesg | grep -i -e tipo = 1300 -e tipo = 1400
$ semodule -DB
$ emodule -B
$ sealert -l "*"

# NGINX - Selinux
$ ps auZ | grep nginx
$ semanage permissive -a httpd_t (Deshabilitando temporalmente SELinux)
$ sudo chcon -R -t httpd_sys_content_t /var/www/html
$ sudo chcon -R -t httpd_sys_rw_content_t /var/www/html

# Firewall
$ sudo firewall-cmd --permanent --zone=public --add-service=http
$ sudo firewall-cmd --permanent --zone=public --add-service=https
$ sudo firewall-cmd --reload
```

## Popup en Terminal para Shellscript

```bash
$ whiptail --yesno "Did you already know whiptail?" 40 150
```

## Eliminar Archivo sin Posibilidad a Recuperar

```bash
$ shred -u file.txt
```

## MOC Configuración en Fedora

```bash
# vim ~/.moc/config
TiMidity_Config = /etc/timidity.cfg
Theme = transparent-background 
XTermTheme = transparent-background
# Privilegios
$ chmod 644 config
```

## Crear Archivo con CAT

```bash
$ cat <<EOF >> shell.sh
#!/bin/bash
echo "Template"
sed -e
df -h
EOF
```

## Deshabilitar Cloud Init en Ubuntu Server

```bash
# Primera opción
$ sudo touch /etc/cloud/cloud-init.disabled
$ sudo vim /etc/cloud/cloud-init.disabled
cloud-init=disabled
# Segunda opción
$ sudo systemctl disable cloud-init.service
$ sudo systemctl stop cloud-init.service
```

## Referencias

- [**Gsettings**](https://manpages.ubuntu.com/manpages/jammy/man1/gsettings.1.html)
- [**SSH**](https://help.ubuntu.com/community/SSH/OpenSSH/PortForwarding)