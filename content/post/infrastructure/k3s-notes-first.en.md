+++
title = "K3s Notes - Part 1"
author = "Fcch"
date = "2021-07-15"
description = "Notes for Kubernetes with K3s on Raspberry Pi"
featured = true
tags = [
    "kubernetes",
    "k3s",
    "docker"
]
categories = [
    "Infrastructure",
]
series = ["Servers"]
thumbnail = "images/k3s-kubernetes/k3s-kubernetes-part-1.png"
+++

Experimenting with [**Docker Swarm**](https://docs.docker.com/engine/swarm/), I got to the task of building something with the [**Raspberry Pi (RPI v3, RPI v4)**](https://www.raspberrypi.org/) that I have in my lab, after googling a lot and chatting with my friend [**Sergio**](https://twitter.com/donkeysharp), he recommended using [**K3s**](https://k3s.io/) which is a [**Kubernetes**](https://kubernetes.io/) distribution with backend [**sqlite3**](https://www.sqlite.org/index.html) based lightweight storage system compatible with [**ARM**](https://en.wikipedia.org/wiki/ARM_architecture) architecture.

<!--more-->

![](/images/k3s-kubernetes/k3s-kubernetes-part-1.png)

I reviewed the official [**K3s**](https://rancher.com/docs/k3s/latest/en/) documentation and I was able to set up my laboratory, the type of exercise that will be developed will be a **Server node** and four **Worker nodes** with the [**sqlite3**](https://www.sqlite.org/index.html) database.

## Architecture

There are two types of nodes:

1. **Server node**, it is the node that runs **K3s server**.
2. **Worker node**, is the node that runs the **K3s agent**.

There are also two ways of implementation:

1. **Single-server Setup with an Embedded DB**, in this configuration, each agent node is registered on the same **Server node**.

![](/images/k3s-kubernetes/k3s-architecture-single-server.png)

2. **High-Availability K3s Server with an External DB**, Which consists of:

  - Two or more **Server nodes** that will serve the **Kubernetes** API and run other control plane services.
  - An **external data store** (as opposed to the built-in SQLite data store used in single server configurations).

![](/images/k3s-kubernetes/k3s-architecture-ha-server.png)

2.1 **Fixed Registration Address for Agent Nodes**

In the high availability server configuration, each node must also register with the **Kubernetes** API using a fixed registration address, after registration, the agent nodes establish a connection directly with one of the **Server nodes**.

![](/images/k3s-kubernetes/k3s-production-setup.svg)

## Previous Configurations

- [**Necessary RPI configurations**](https://rancher.com/docs/k3s/latest/en/advanced/#enabling-legacy-iptables-on-raspbian-buster): Before installing the **K3s** binaries it is necessary to make some extra configurations in the [**Raspberry Pi**](https://www.raspberrypi.org/) operating system, we will use **Raspbian Buster** for this laboratory.

- **Enabling legacy iptables on Raspbian Buster**

**Raspbian Buster** defaults to using **nftables** instead of **iptables**. **K3s** networking features require **iptables** and do not work with **nftables**. Follow the steps below to switch configure Buster to use legacy **iptables**:

```bash
$ sudo iptables -F
$ sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
$ sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
$ sudo reboot
```

- **Enabling cgroups for Raspbian Buster**

Standard **Raspbian Buster** installations do not start with **cgroups** enabled. **K3s** needs **cgroups** to start the systemd service. **cgroups** can be enabled by appending **cgroup_memory=1** and **cgroup_enable=memory** to **/boot/cmdline.txt**.

```text
# Inside the file cmdline.txt
console=serial0,115200 console=tty1 root=PARTUUID=58b06195-02 rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait cgroup_memory=1 cgroup_enable=memory
```

With these changes applied, the operating system must be restarted on the **Raspberry Pi**.

## Install Server node

According to the official documentation we can use the official scripts to install the binaries for the **K3s** **Server node**:

```bash
$ curl -sfL https://get.k3s.io | sh -
```

This script will install all the necessary tools such as:

- **kubectl**
- **crictl**
- **ctr**
- **k3s-killall.sh**
- **k3s-uninstall.sh**

It will also create the configuration file **/etc/rancher/k3s/k3s.yaml**

## Install Worker Nodes

After the installation of the **K3s** server we can add nodes:

```bash
$ curl -sfL https://get.k3s.io | K3S_URL=https://myserver:6443 K3S_TOKEN=mynodetoken sh -
```

where:

- **K3S_URL**: - **K3S_URL**: This is the IP address or domain of the  **K3s** server.
- **K3S_TOKEN**: It is a token that is stored in the **K3s** server, **/var/lib/rancher/k3s/server/node-token**
- The hostname of the new nodes must be different.

## Basic Operations

We can execute the common **Kubernetes** commands or use the **K3s** command, to these two commands we must prepend the **sudo** command so that the orders are executed without problems.

```bash
$ sudo k3s kubectl get nodes
# or also
$ sudo kubectl get nodes
$ sudo kubectl get pods --all-namespaces
```

Daemon control:

```bash
$ sudo systemctl status k3s
$ sudo systemctl stop k3s
```

## Artículos K3s

1. [**K3s - Part 1**](https://blog.fcch.xyz/en/post/infrastructure/k3s-notes-first/)
2. [**K3s - Part 2**](https://blog.fcch.xyz/en/post/infrastructure/k3s-notes-second/)
3. [**K3s - Part 3**](https://blog.fcch.xyz/en/post/infrastructure/k3s-notes-third/)
4. [**K3s - Part 4**](https://blog.fcch.xyz/en/post/infrastructure/k3s-notes-fourth/)

## References

- [**Quick Start**](https://rancher.com/docs/k3s/latest/en/quick-start/)
- [**Server Config**](https://rancher.com/docs/k3s/latest/en/installation/install-options/server-config/)
- [**Install Options**](https://rancher.com/docs/k3s/latest/en/installation/install-options/)
- [**Kube Dashboard**](https://rancher.com/docs/k3s/latest/en/installation/kube-dashboard/)
- [**Docs K3S**](https://rancher.com/docs/)
- [**Official Website**](https://k3s.io/)
- [**Architecture**](https://rancher.com/docs/k3s/latest/en/architecture/)