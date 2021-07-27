+++
title = "K3s Apuntes - Parte 2"
author = "Fcch"
date = "2021-07-24"
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
thumbnail = "images/k3s-kubernetes/k3s-kubernetes-part-2.png"
+++

We continue with the tests in our small cluster with [**K3s**](https://k3s.io/) built on the [**Raspberry Pi (RPI v3, RPI v4)**](https://www.raspberrypi.org/) that I have in my laboratory, for the tests we use the official [**K3s - Rancher**](https://rancher.com/docs/k3s/latest/en/) documentation and also the official [**Kubernetes**](https://kubernetes.io/docs/tutorials/kubernetes-basics/) documentation.

<!--more-->

![](/images/k3s-kubernetes/k3s-rpi-v3-v4.jpg)

In the image we have as a detail:

1. **Three Raspberry Pi 4, Model B** (1 Server Node and 2 Worker Nodes).
2. **Two Raspberry Pi 3, Model B** (2 Worker Nodes,).
3. One **8-port Tp-Link TL-SG1008D Gigabit Desktop Switch**.

## Basic concepts

With the Kubernetes components:

![](/images/k3s-kubernetes/components-of-kubernetes.png)

We can describe some important components:

1. **kube-apiserver**: The API server is a component of the Kubernetes control plane that exposes the Kubernetes API. The API server is the front end for the Kubernetes control plane.
2. **etcd**: Consistent, highly available *key-value* database used as the Kubernetes backing store for all data in the cluster.
3. **kube-scheduler**: A control plane component that searches for newly created pods without an assigned node and selects a node to run.
4. **kube-controller-manager**: Control plane component that runs controller processes.

    - *Node controller*: Responsible for noticing and responding when nodes go down.
    - *Job controller*: Watches for Job objects that represent one-off tasks, then creates Pods to run those tasks to completion.
    - *Endpoints controller*: Populates the Endpoints object (that is, joins Services & Pods).
    - *Service Account & Token controllers*: Create default accounts and API access tokens for new namespaces.

5. **cloud-controller-manager**: A Kubernetes control plane component that incorporates cloud-specific control logic (runs in a single process with *kube-controller-manager*).

    - *Node controller*: For checking the cloud provider to determine if a node has been deleted in the cloud after it stops responding.
    - *Route controller*: For setting up routes in the underlying cloud infrastructure.
    - *Service controller*: For creating, updating and deleting cloud provider load balancers.

6. **kubelet**: An agent running on each node in the cluster ensures that the containers run on a Pod.
7. **kube-proxy**: It is a network proxy that runs on every node in your cluster, implementing part of the Kubernetes service concept.
8. **container runtime**: The container runtime is the software responsible for running the containers.
9. **kube-scheduler**: A control plane component that searches for newly created pods without an assigned node and selects a node to run.

## Install Kubectl

In order to work with our [K3s](https://k3s.io/) **RPI** cluster, we can work from the **Server Node** or install [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/) on our personal computer, in this case we will install the package for a [GNU/Linux](https://www.gnu.org/home.en.html) distribution, [**Debian Buster 10**](https://debian.org), adding the repository as follows:

```bash
# Update and install the necessary packages
$ sudo apt-get update
$ sudo apt-get install -y apt-transport-https ca-certificates curl
# We download the public signature key
$ sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
# Add the official repository
$ echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
# Update again and install the package
$ sudo apt install update
$ sudo apt install -y kubectl
```

## Kubectl Configuration with Cluster K3s

To work from our personal computer, we have to create a configuration file inside the **.kube** folder, the configuration file must have the name **config**, the folder and the file must be created with the appropriate permissions :

```bash
$ mkdir ~/.kube
$ touch ~/.kube/config
$ chmod 775 ~/.kube
$ chmod 420 ~/.kube/config
```

The content of the **config** file should look like this:

```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: CERTIFICATE_CONTENT
    server: https://IP_OR_DOMAIN:6443
  name: default
contexts:
- context:
    cluster: default
    user: default
  name: default
current-context: default
kind: Config
preferences: {}
users:
- name: default
  user:
    client-certificate-data: CERTIFICATE_CONTENT
    client-key-data: CERTIFICATE_CONTENT_KEY
```

This data can be obtained from the **Server Node**, exactly the data is in **/etc/rancher/k3s/k3s.yaml**.

## Testing After Installation

From our local computer we can execute the following commands:

```bash
$ kubectl version
# If there is a problem with the previous command we can execute:
$ kubectl version --client=true
```

If everything is correct we will have an output similar to this:

![](/images/k3s-kubernetes/kubectl-version-tests-v2.png)

Comandos útiles para iniciar:

```bash
# Obtener ayuda
$ kubectl --help
# Listar todos los contextos en su archivo kubeconfig
$ kubectl config get-contexts
# Listar todos los nodos disponibles
$ kubectl get nodes
```

## Referencias

- [**Quick start**](https://rancher.com/docs/k3s/latest/en/quick-start/)
- [**Kube Dashboard**](https://rancher.com/docs/k3s/latest/en/installation/kube-dashboard/)
- [**Docs K3S**](https://rancher.com/docs/)
- [**Kubernetes Concepts**](https://kubernetes.io/docs/concepts/_print/)
- [**Docs Kubernetes**](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
- [**etcd**](https://etcd.io/)
- [**Cluster Admin Access**](https://rancher.com/docs/rancher/v2.x/en/cluster-admin/cluster-access/kubectl/)
- [**Cluster Accesss**](https://rancher.com/docs/k3s/latest/en/cluster-access/)