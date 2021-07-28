+++
title = "K3s Notes - Part 3"
author = "Fcch"
date = "2021-07-27"
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
thumbnail = "images/k3s-kubernetes/k3s-kubernetes-part-3.png"
+++

In [**Parte 2**](https://blog.fcch.xyz/post/infrastructure/k3s-notes-second/) of [**K3s**](https://k3s.io/), a description of various concepts that are handled in [**Kubernetes**](https://kubernetes.io/) was made, the [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/) client was installed and connectivity tests to the [**Kubernetes**](https://kubernetes.io/) API were done.

<!--more-->

![](/images/k3s-kubernetes/k3s-rpi-part-3.jpg)

Now we will execute basic tasks in our small cluster, but before we continue describing more details of **Kubernetes**.

## Kubernetes objects

1. **Pod**: It is a group of one or more containers (such as Docker containers), with shared storage/network, and some specifications of how to run the containers.
2. **Service**: An abstract way to expose an application running on a set of Pods as a network service.
3. **Volume**: It has a functionality similar to that of [**Docker Volume**](https://docs.docker.com/storage/volumes/), where data persistence is searched after restarting one container.
2. **Namespace**: It allows us to isolate resources for the use of the different users of the cluster.
3. **Manifest**: It is a **yaml** manifest file that contains instructions that specify how to deploy an application to the node or nodes in a **Kubernetes** cluster.

## Kubernetes Drivers

**Kubernetes** contains top-level abstractions called Controllers. The Controllers are based on the basic objects and provide additional functionality on top of them.

1. **Replicaset**: The purpose of a ReplicaSet is to keep a stable set of replicas of Pods running at all times. Thus, it is used numerous times to ensure the availability of a specific number of identical Pods.
2. **Deployment**: A Deployment controller provides declarative updates for Pods and ReplicaSets. When you describe the desired state on a Deployment object, the Deployment controller takes care of changing the current state to the desired state in a controlled way. You can define Deployments to create new ReplicaSets, or remove existing Deployments and adopt all their resources with new Deployments.
3. **StatefulSet**: A StatefulSet is the workload API object used to manage stateful applications.
4. **DaemonSet**: A DaemonSet ensures that all (or some) of the nodes run a copy of a Pod. As more nodes are added to the cluster, new Pods are added to them. As nodes are removed from the cluster, those Pods are destroyed. Deleting a DaemonSet clears all the Pods that have been created.
5. **Job**: A Job creates one or more Pods and ensures that a specific number of them finish successfully. As pods complete successfully, the Job tracks successful executions. When a specified number of successful executions is reached, the task (that is, the Job) is completed. Deleting a Job removes the Pods you have created.

## Commands Kubernetes

Some common commands:

```bash
# List the available namespaces
$ kubectl get ns
# List Pods available in a Namespace
$ kubectl -n kube-system get pods
# List Pods available in a Namespace with more details
$ kubectl -n kube-system get pods -o wide
# Delete a pod
$ kubectl -n kube-system delete pod NAME_RUN_POD
```

As an extra detail if there is a problem with the configuration file to the node we can execute:

```bash
$ export KUBECONFIG=PATH_CONFIG_FILE
```

## Yaml File Examples

**1.** For our first example we will create and apply a basic file:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: fcch-nginx
spec:
  containers:
  - name: fcch-nginx
    image: nginx:alpine
```

To execute the content of the **yaml** files we must know some commands:

```bash
$ kubectl apply -f basic_pod.yaml
```

To enter the container inside the **pod** we can execute:

```bash
$ kubectl exec -it fcch-nginx -- sh
```

To remove the **pod** created:

```bash
$ kubectl delete pod fcch-nginx
# Verificate pods 
$ kubectl get pods
```

**2.** For this example we will use environment variables and allocate resources.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: fcch-nginx
spec:
  containers:
  - name: fcch-nginx
    image: nginx:alpine
    env:
    - name: NAME_VARIABLE
      value: "fcch"
    - name: NAME_VARIABLE_OTHER
      value: "fcch-blog"
    - name: DD_AGENT_HOST
      valueFrom:
        fieldRef:
          fieldPath: status.hostIP
    resources:
      requests:
        memory: "64Mi"
        cpu: "200m"
      limits:
        memory: "128Mi"
        cpu: "500m"
    readinessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 10
    livenessProbe:
      tcpSocket:
        port: 80
      initialDelaySeconds: 15
      periodSeconds: 20
    ports:
    - containerPort: 80
```

If we want to verify the **yaml** file that was executed we can execute:

```bash
$ kubectl get pod fcch-nginx -o yaml
```

If you need to see the details of a **pod**:

```bash
$ kubectl describe pod fcch-nginx
```

**3.** In this example we will use volumes, the type will be **StatefulSet** and we will use **replicas**.

A detail before continuing, **PVC**: Persistent Volume Claim.

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: app-test
spec:
  selector:
    matchLabels:
      app: apppod
  serviceName: "first-frontend"
  replicas: 2
  template:
    metadata:
      labels:
        app: apppod
    spec:
      containers:
      - name: first-frontend
        image: busybox
        args:
        - sleep
        - infinity
        volumeMounts:
        - mountPath: "/data"
          name: app-pvc
  volumeClaimTemplates:
  - metadata:
      name: app-pvc
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 5Gi
      storageClassName: do-block-storage
```

To work with **Volumes** we can execute the following commands:

```bash
# List volumes
$ kubectl get pvc
# Show details PVC
$ kubectl describe pvc app-pvc-app-test-0
# List Pods of type StatefulSet
$ kubectl get statefulsets
# List Pods of type StatefulSet
$ kubectl get stss
# Delete Pod of type StatefulSet
$ kubectl delete sts app-test
# Delete PVC
$ kubectl delete pvc app-pvc-app-test-0
```

Some commands that can help us:

```bash
# List all
$ kubectl get all
# List services
$ kubectl get svc
# Service detail
$ kubectl describe svc kubernetes
```

## Artículos K3s

1. [**K3s - Parte 1**](https://blog.fcch.xyz/post/infrastructure/k3s-notes-first/)
2. [**K3s - Parte 2**](https://blog.fcch.xyz/post/infrastructure/k3s-notes-second/)
3. [**K3s - Parte 3**](https://blog.fcch.xyz/post/infrastructure/k3s-notes-third/)

## Referencias

- [**Inicio Rápido**](https://rancher.com/docs/k3s/latest/en/quick-start/)
- [**Kube Dashboard**](https://rancher.com/docs/k3s/latest/en/installation/kube-dashboard/)
- [**Docs K3S**](https://rancher.com/docs/)
- [**Conceptos Kubernetes**](https://kubernetes.io/es/docs/concepts/)
- [**Docs Kubernetes**](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
