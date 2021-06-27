+++
title = "K3s Apuntes - Parte 1"
author = "Fcch"
date = "2021-06-26"
description = "Apuntes para Kubernetes con K3s en Raspberry Pi"
featured = true
tags = [
    "kubernetes",
    "k3s",
    "docker"
]
categories = [
    "Infraestructura",
]
series = ["Servidores"]
thumbnail = "images/k3s-kubernetes/k3s-kubernetes.png"
+++

Experimentando con [**Docker Swarm**](https://docs.docker.com/engine/swarm/) me puse a la tarea de armar algo con los [**Raspberry Pi (RPI v3, RPI v4)**](https://www.raspberrypi.org/) que tengo en mi laboratorio, luego de estar googleando y charlando con [**Sergio**](https://twitter.com/donkeysharp), me recomendó usar [**K3s**](https://k3s.io/) que es una distribución de [**Kubernetes**](https://kubernetes.io/) con backend de almacenamiento ligero basado en [**sqlite3**](https://www.sqlite.org/index.html) compatible con arquitectura [**ARM**](https://en.wikipedia.org/wiki/ARM_architecture).

<!--more-->

![](/images/k3s-kubernetes/k3s-kubernetes.png)

Revisamos la documentación oficial de [**K3s**](https://rancher.com/docs/k3s/latest/en/) y pude armar mi laboratorio, el tipo de ejercicio que se desarrollara sera un **Server node** y cuatro **Worker nodes** con la  base de datos [**sqlite3**](https://www.sqlite.org/index.html).

## Arquitectura

Existen dos tipos de nodos:

1. **Server node**, es el nodo que ejecuta **k3s server**.
2. **Worker node**, es el nodo que ejecuta **k3s agent**.

También existen dos formas de implementación:

1. **Un solo servidor con una base de datos integrada**, en esta configuración, cada nodo de agente está registrado en el mismo **Server node**.

![](/images/k3s-kubernetes/k3s-architecture-single-server.png)

2. **Servidor K3s de Alta Disponibilidad con una base de datos externa**, que se compone de:

   - Dos o más **nodos de servidor** que servirán a la API de **Kubernetes** y ejecutarán otros servicios del plano de control.
   - Un **almacén de datos externo** (a diferencia del almacén de datos SQLite incorporado que se usa en configuraciones de un solo servidor).

![](/images/k3s-kubernetes/k3s-architecture-ha-server.png)

2.1 **Direcciones de registro fija para nodos agente**

En la configuración del servidor de alta disponibilidad, cada nodo también debe registrarse con la API de **Kubernetes** mediante una dirección de registro fija, después del registro, los nodos del agente establecen una conexión directamente con uno de los **Server nodes**.

![](/images/k3s-kubernetes/k3s-production-setup.svg)

## Configuraciones Previas

- [**Configuraciones necesarias RPI**](https://rancher.com/docs/k3s/latest/en/advanced/#enabling-legacy-iptables-on-raspbian-buster): Antes de instlar los binarios de [**K3s**](https://k3s.io/) es necesario realizar unas configuraciones extra en el sistema operativo de las [**Raspberry Pi**](https://www.raspberrypi.org/), utilizaremos **Raspbian Buster** para este laboratorio.

- **Habilitar iptables heredados en Raspbian Buster**

**Raspbian Buster** utiliza de forma predeterminada en **nftables** en lugar de **iptables**. Las funciones de red de **K3s** requieren **iptables** y no funcionan con **nftables**, para solucionar este problema debemos realizar el cambio correspondiente.

```bash
$ sudo iptables -F
$ sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
$ sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
$ sudo reboot
```

- **Habilitando cgroups para Raspbian Buster**

Las instalaciones estándar de **Raspbian Buster** no se inicializan con **cgroups** habilitado. **K3S** necesita **cgroups** inicializado como un servicio systemd. Se puede habilitar **cgroups** agregando **cgroup_memory=1** y **cgroup_enable=memory** en **/boot/cmdline.txt**.

```text
# Dentro del archivo cmdline.txt
console=serial0,115200 console=tty1 root=PARTUUID=58b06195-02 rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait cgroup_memory=1 cgroup_enable=memory
```

Con estos cambios aplicados se debe reiniciar el sistema operativo en los **Raspberry Pi**.

## Instalación del Server node

Según la documentación oficial podemos utilizar los scripts oficiales para instalar los binarios para el **Server node** de **K3s**:

```bash
$ curl -sfL https://get.k3s.io | sh -
```

Este script instalará todas la herramientas necesarias como: 

- kubectl
- crictl
- ctr
- k3s-killall.sh
- k3s-uninstall.sh

También creara el archivo de configuración **/etc/rancher/k3s/k3s.yaml**

## Instalación de Worker Nodes

Luego de la instalación del servidor **K3s** podemos agregar nodos: 

```bash
$ curl -sfL https://get.k3s.io | K3S_URL=https://myserver:6443 K3S_TOKEN=mynodetoken sh -
```

donde:

- **K3S_URL**: Esta es la dirección IP o el dominio del servidor **K3s**.
- **K3S_TOKEN**: Es un token que se almacena en el servidor **K3s**, **/var/lib/rancher/k3s/server/node-token**
- Es necesario que el hostname de los nuevos nodos sean diferentes.

## Operaciones Básicas

Podemos ejecutar los comandos comunes de **Kubernetes** o utilizar el comando **K3s**, a estos dos comandos debemos anteponerle el comando **sudo** para que se ejecuten las ordenes sin problemas.

```bash
$ sudo k3s kubectl get nodes
# o también
$ sudo kubectl get nodes
$ sudo kubectl get pods --all-namespaces
```

Control daemon:

```bash
$ sudo systemctl status k3s
$ sudo systemctl stop k3s
```

## Referencias

- [**Quick Start**](https://rancher.com/docs/k3s/latest/en/quick-start/)
- [**Server Config**](https://rancher.com/docs/k3s/latest/en/installation/install-options/server-config/)
- [**Install Options**](https://rancher.com/docs/k3s/latest/en/installation/install-options/)
- [**Kube Dashboard**](https://rancher.com/docs/k3s/latest/en/installation/kube-dashboard/)
- [**Docs K3S**](https://rancher.com/docs/)
- [**Official Website**](https://k3s.io/)
- [**Architecture**](https://rancher.com/docs/k3s/latest/en/architecture/)