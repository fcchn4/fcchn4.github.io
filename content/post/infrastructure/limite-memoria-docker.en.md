+++
title = "Memory Limit in Docker"
author = "Fcch"
date = "2020-08-12"
description = "Servers and Infrastructure Guide"
featured = false
tags = [
    "docker",
    "grub",
    "cgroup"
]
categories = [
    "Infrastructure",
]
series = ["Servers"]
thumbnail = "images/docker-limite-mem/kernel-panic.jpg"
+++

When we create a new container, it has unlimited access to system resources, so if one occupies all the memory, the other containers will be affected or, failing that, the operating system runs out of resources.

Docker has the ability to apply a memory limit to a specific container.

<!--more-->

![](/images/docker-limite-mem/kernel-panic.jpg)

## Creating a Container

```cmd
$ docker run --name dell -it debian bash
```

## Obtaining Container Information

```cmd
$ docker inspect dell | grep -i 'Memory'
```

![](/images/docker-limite-mem/docker-memory.png)

For **"Memory": 0** the value **0**  indicates that the container doesn't have a limited memory value, that is, it will use everything that the host has available, possibly affecting other containers that are within the same host.

## Update Container Memory

```cmd
$ docker update -m 256MB dell
```

In testing there were problems on Debian and Ubuntu operating systems:

![](/images/docker-limite-mem/docker-kernel-fail.png)

This problem is due to the fact that the [**cgroup**](https://en.wikipedia.org/wiki/Cgroups) is not mounted on the system, to mount it we edit the file **grub**.

```cmd
$ sudo vim /etc/default/grub
    ...
    GRUB_CMDLINE_LINUX_DEFAULT="cgroup_enable=memory swapaccount=1"
    GRUB_CMDLINE_LINUX="cgroup_enable=memory swapaccount=1"
    ...
```

Update **grub** and restart system:

```cmd
$ sudo update-grub
$ sudo update-grub2
$ sudo reboot
```

Finally we run the command to update the memory again and check the memory status:

![](/images/docker-limite-mem/docker-update-mem.png)

## References

- [**Fedora Magazine**](https://fedoramagazine.org/docker-and-fedora-32/)
- [**Fedora Wiki**](https://fedoraproject.org/wiki/Changes/CGroupsV2)