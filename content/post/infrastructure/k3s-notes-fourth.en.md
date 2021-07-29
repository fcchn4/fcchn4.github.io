+++
title = "K3s Notes - Part 4"
author = "Fcch"
date = "2021-07-28"
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
thumbnail = "images/k3s-kubernetes/k3s-kubernetes-part-4.png"
+++

To finish the small lab and finish the last part of [**K3s**](https://k3s.io/), we will name some important details, install and create the service for [**Kubernetes Dashboard**](https://rancher.com/docs/k3s/latest/en/installation/kube-dashboard/) and finally we will list some interesting tools.

<!--more-->

![](/images/k3s-kubernetes/k3s-rpi-part-4.jpg)

## Pod Networking

Each Pod is assigned a unique IP address for each address family. Each container in a Pod shares the network namespace, including the IP address and network ports. Inside a Pod (and only then), the containers that belong to the Pod can communicate with each other using localhost. When containers in a Pod communicate with entities outside the Pod, they must coordinate how they use shared network resources (such as ports). Inside a Pod, containers share an IP address and port space, and can find each other via localhost. The containers in a Pod can also communicate with each other using standard interprocess communications, such as SystemV semaphores or POSIX shared memory. Containers in different Pods have different IP addresses and cannot communicate over IPC without special configuration. Containers that want to interact with a container running on a different Pod can use IP networks to communicate.

**CNI - Cluster Networking Interface**: It is a network framework that allows dynamic configuration of network resources through a group of libraries and specifications written by Go. The aforementioned specification for the plug-in describes an interface that would configure the network, provision IP addresses, and maintain connectivity for multiple hosts.

In the context of Kubernetes, the CNI integrates seamlessly with the kubelet to allow automatic network configuration between pods using an underlying or overlay network. An underlying network is defined at the physical level of the network layer made up of routers and switches.

**Calico**: It is an open source network and network security solution for containers, virtual machines, and native host-based workloads. Calico supports several data planes, including: a pure Linux eBPF data plane, a standard Linux network data plane, and a Windows HNS data plane. Calico provides a complete network stack, but can also be used in conjunction with cloud provider CNIs to provide network policy enforcement.

## Kubernetes Services

1. **Cluester IP**: Assign a Fixed IP within a cluster, it works with a small Load Balancer.
2. **Node Port**: Similar to the previous one, but at the port level, it creates a port on each node that receives all the traffic and directs it to the desired service.
3. **Load Balancer**: Create a Load Balancer in a cloud provider and redirect the Pods traffic.
4. **Ingress**: It exposes HTTP and HTTPS routes from outside the cluster to services within the cluster, the routing of the traffic is controlled by rules defined in the Ingress resource.

## Simple Example

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: fcch-app
spec:
  rules:
  - http:
      paths:
      - path: /v1
        pathType: Prefix
        backend:
          service:
            name: fcch-v1
            port:
              number: 8080
      - path: /v2
        pathType: Prefix
        backend:
          service:
            name: fcch-v2
            port:
              number: 8080
```

Some useful commands:

```bash
# List Ingress types
$ kubectl get ing
# List services
$ kubectl -n new-ingress get svc
```

## Dashboard Kubernetes

K3s provides us with a simple way to install the Dashboard, you just have to execute the following commands:

```bash
GITHUB_URL=https://github.com/kubernetes/dashboard/releases
VERSION_KUBE_DASHBOARD=$(curl -w '%{url_effective}' -I -L -s -S ${GITHUB_URL}/latest -o /dev/null | sed -e 's|.*/||')
sudo k3s kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/${VERSION_KUBE_DASHBOARD}/aio/deploy/recommended.yaml
```

The files should be created:

**dashboard.admin-user.yml**

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
```

**dashboard.admin-user-role.yml**

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

Then the command must be executed to apply the **yaml** files.

```bash
$ sudo k3s kubectl create -f dashboard.admin-user.yml -f dashboard.admin-user-role.yml
```

We have to obtain the token to be able to enter the dashboard with the user **admin-user**:

```bash
$ sudo k3s kubectl -n kubernetes-dashboard describe secret admin-user-token | grep '^token'
```

Finally we can expose the service locally with:

```bash
$ sudo k3s kubectl proxy
```

If we need to edit the **Namespace** of **kubernetes-dashboard** we can execute:

```bash
$ kubectl -n kubernetes-dashboard edit service kubernetes-dashboard
$ kubectl -n kubernetes-dashboard get services
```

In the example the **Server Node** is without a graphical environment, and for the example an SSH tunnel was created for port 8001:

```bash
$ ssh -L 8001:127.0.0.1:8001 -N -f -l USER IP-DOMAIN
```

And to enter the **Dashboard** from the web browser the path would be:

```bash
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/
```

If everything ran without problems our **Dashboard** would be similar to this:

- Nodes List

![](/images/k3s-kubernetes/kubernetes-dashboard-1.png)

- Namespaces List

![](/images/k3s-kubernetes/kubernetes-dashboard-2.png)

- Dashboard Configuration

![](/images/k3s-kubernetes/kubernetes-dashboard-3.png)

Comandos útiles:

```bash
$ kubectl config view
$ kubectl get services --all-namespaces
$ kubectl -n kubernetes-dashboard edit service kubernetes-dashboard
$ kubectl -n kubernetes-dashboard get svc
```

## Interesting Tools

1. [**Lens**](https://k8slens.dev/): It is a system that takes control of your Kubernetes clusters.
2. [**Kind**](https://kind.sigs.k8s.io/): This is a tool for running local Kubernetes clusters through Docker container "nodes".
3. [**Minikube**](https://minikube.sigs.k8s.io/docs/start/): It is a local Kubernetes and focuses on facilitating learning and development for Kubernetes.
4. [**Helm**](https://helm.sh/): Functions like a packaging manager for Kubernetes, Helm is the best way to find, share, and use software built for Kubernetes.
5. [**Supervisord**](http://supervisord.org/): It is a client/server system that allows its users to monitor and control a series of processes in operating systems similar to UNIX (It is not recommended to run two processes per container, but if necessary we can use this tool).

## Artículos K3s

1. [**K3s - Part 1**](https://blog.fcch.xyz/en/post/infrastructure/k3s-notes-first/)
2. [**K3s - Part 2**](https://blog.fcch.xyz/en/post/infrastructure/k3s-notes-second/)
3. [**K3s - Part 3**](https://blog.fcch.xyz/en/post/infrastructure/k3s-notes-third/)
4. [**K3s - Part 4**](https://blog.fcch.xyz/en/post/infrastructure/k3s-notes-fourth/)

## Referencias

- [**Quick Start**](https://rancher.com/docs/k3s/latest/en/quick-start/)
- [**Kube Dashboard**](https://rancher.com/docs/k3s/latest/en/installation/kube-dashboard/)
- [**Docs K3S**](https://rancher.com/docs/)
- [**Concepts Kubernetes**](https://kubernetes.io/es/docs/concepts/)
- [**Docs Kubernetes**](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
- [**Kubernetes Proxy**](https:kubernetes-dashboard:/proxy/)