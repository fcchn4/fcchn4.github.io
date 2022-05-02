+++
title = "Simple Usual Commands"
author = "Fcch"
date = "2022-05-01"
description = "List of commonly used commands"
featured = true
tags = [
    "cmd",
]
categories = [
    "notes",
]
series = ["Linux"]
thumbnail = "images/usual-cmd/linux-cmd.jpg"
+++

Every day we learn commands that allow us to perform certain tasks at work, so as not to forget any of these commands that served, create a list as a memory aid.

<!--more-->

![](/images/usual-cmd/linux-cmd-post.jpg)

## Check OS Install Date

```bash
# First option
$ ls -lct /etc | tail -1 | awk '{print $6, $7, $8}'
# Second option
$ ls -lct --time-style=+"%d-%m-%Y %H:%M:%S" /etc | tail -1 | awk '{print $6, $7, $8}'
# Simple Option
$ ls -lAhF /etc/hostname
```

## Identify the Name of a Service Based on the Port

```bash
# We identify the process ID
$ fuser -n tcp 7070
# We look for the process name based on the ID
$ ps aux | grep ID_PROCESS
```

## Identificar Detalle de Hardware

```bash
$ inxi -Fxz
# Common: lsblk, lsusb, lsscsi, lspci, lscpu
$ hwinfo --short
$ free -m
$ cat /proc/partitions
$ sudo hdparm -i /dev/sda # Can change sda
$ ls /sys/class/net/
$ ls /sys/class/net/enp0s25 # You can change enp0s25
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

## List Open Ports

```bash
# With netstat
$ netstat -vatn # netstat -tlpn
# With ss
$ ss -tlpn
```

## For Debian OS and Derivatives, Language and Time Zone Settings

```bash
# vim /etc/environment
LC_ALL="es_BO.UTF-8"
# Timezone configuration
$ timedatectl set-timezone America/La_Paz
```

## DNF Commands Tested on Fedora

```bash
# Remove Old Fedora Kernel:
$ dnf remove $(dnf repoquery --installonly --latest-limit=-2 -q)

# Remove Old CentOS Kernel:
$ package-cleanup --oldkernels --count=2

# RPM Commands:
$ rpm -qa kernel\* | sort -V

# Configuration file:
## You must edit the /etc/yum.conf file for CentOS and /etc/dnf/dnf.conf 
## for Fedora and set installonly_limit:
$ installonly_limit=2

# Regenerate Kernel Modules
$ dracut --regenerate-all --force
```

## Gnome Settings with Gsettings

```bash
# Gnome Shell Customization to normal:
$ gsettings set org.gnome.shell.extensions.dash-to-dock extend-height false
$ gsettings set org.gnome.shell.extensions.dash-to-dock transparency-mode 'FIXED'
$ gsettings set org.gnome.shell.extensions.dash-to-dock background-opacity 0.0
```

## SSH Commands

```bash
# SSH Tunneling
$ ssh -L 2228:hostname.IP_DOMAIN_PUBLIC:22 user@IP_DOMAIN_PRIVATE
$ ssh -f user@IP_DOMAIN_PRIVATE -L 2229:hostname.IP_DOMAIN_PRIVATE:22 -N
$ ssh -L 2229:hostname.IP_DOMAIN_PUBLIC:22 user@IP_DOMAIN_PRIVATE

# Examples
$ ssh -i .ssh/PUB_KEY -f user@IP_DOMAIN_PUBLIC -L 2228:IP_DOMAIN_PRIVATE:22 -N
$ ssh -i .ssh/PUB_KEY user@localhost -p2228

# SSH Agent TTY
$ eval $(ssh-agent -s)
$ ssh-add ~/.ssh/name_priv_key

# List of users with accepted SSH Login
$ sudo cat /var/log/auth* | grep Accepted
```

## Useful Commands

```bash
# OpenWRT Package Manager
$ opkg list-upgradable | cut -f 1 -d ' ' | xargs opkg upgrade 

# Update font cache
$ fc-cache -f -v

# Remount Linux Device
$ mount -o remount,rw -t ext4 /dev/sdb /media/device

# Set all subfolders of a folder to 755:
$ find . -type d -exec chmod 755 {} \;

# All files to 644:
$ find . -type f -exec chmod 644 {} \;

# Set only files ending with .php to 644:
$ find . -name \*\.php -exec chmod 644 {} \;
```

## SELinux and Firewall CMD

```bash
$ journalctl -t setroubleshoot
$ dmesg | grep -i -e tipo = 1300 -e tipo = 1400
$ semodule -DB
$ emodule -B
$ sealert -l "*"

# NGINX - Selinux
$ ps auZ | grep nginx
$ semanage permissive -a httpd_t (SELinux Disable Temporary)
$ sudo chcon -R -t httpd_sys_content_t /var/www/html
$ sudo chcon -R -t httpd_sys_rw_content_t /var/www/html

# Firewall
$ sudo firewall-cmd --permanent --zone=public --add-service=http
$ sudo firewall-cmd --permanent --zone=public --add-service=https
$ sudo firewall-cmd --reload
```

## Popup in Terminal for Shellscript

```bash
$ whiptail --yesno "Did you already know whiptail?" 40 150
```

## Delete File with No Possibility to Recover

```bash
$ shred -u file.txt
```

## MOC Configuration on Fedora

```bash
# vim ~/.moc/config
TiMidity_Config = /etc/timidity.cfg
Theme = transparent-background 
XTermTheme = transparent-background
# Privilegios
$ chmod 644 config
```

## Create file with CAT

```bash
$ cat <<EOF >> shell.sh
#!/bin/bash
echo "Template"
sed -e
df -h
EOF
```

## Disable Cloud Init on Ubuntu Server

```bash
# First option
$ sudo touch /etc/cloud/cloud-init.disabled
$ sudo vim /etc/cloud/cloud-init.disabled
cloud-init=disabled
# Second option
$ sudo systemctl disable cloud-init.service
$ sudo systemctl stop cloud-init.service
```

## Referencias

- [**Gsettings**](https://manpages.ubuntu.com/manpages/jammy/man1/gsettings.1.html)
- [**SSH**](https://help.ubuntu.com/community/SSH/OpenSSH/PortForwarding)