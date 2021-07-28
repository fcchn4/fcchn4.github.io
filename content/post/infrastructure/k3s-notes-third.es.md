+++
title = "K3s Apuntes - Parte 3"
author = "Fcch"
date = "2021-07-27"
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
thumbnail = "images/k3s-kubernetes/k3s-kubernetes-part-3.png"
+++

En la [**Parte 2**](https://blog.fcch.xyz/post/infrastructure/k3s-notes-second/) de [**K3s**](https://k3s.io/) se hizo una descripción de varios conceptos que se manejan en [**Kubernetes**](https://kubernetes.io/), se instaló el client [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/) y se hizo pruebas de conectividad a el API de [**Kubernetes**](https://kubernetes.io/).

<!--more-->

![](/images/k3s-kubernetes/k3s-rpi-part-3.jpg)

Ahora ejecutaremos tareas básicas en nuestro pequeño cluster, pero antes seguimos describiendo mas detalles de **Kubernetes**.

## Objetos de Kubernetes

1. **Pod**: Es un grupo de uno o más contenedores (como contenedores Docker), con almacenamiento/red compartidos, y unas especificaciones de cómo ejecutar los contenedores.
2. **Service**: Una forma abstracta de exponer una aplicación que se ejecuta en un conjunto de Pods como un servicio de red.
3. **Volume**: Tiene una funcionalidad similar al de [**Docker Volume**](https://docs.docker.com/storage/volumes/), donde se busca persistencia de datos luego de el reinicio de un contenedor.
2. **Namespace**: Nos permite aislar recursos para el uso de los distintos usuarios del cluster.
3. **Manifest**: Es un archivo **yaml** manifiesto que contiene instrucciones que especifican como desplegar una aplicación al nodo o nodos en un cluster de **Kubernetes**.

## Controladores Kubernetes

**Kubernetes** contiene abstracciónes de nivel superior llamadas Controladores. Los Controladores se basan en los objetos básicos y proporcionan funcionalidades adicionales sobre ellos.

1. **Replicaset**: El objeto de un ReplicaSet es el de mantener un conjunto estable de réplicas de Pods ejecutándose en todo momento. Así, se usa en numerosas ocasiones para garantizar la disponibilidad de un número específico de Pods idénticos.
2. **Deployment**: Un controlador de Deployment proporciona actualizaciones declarativas para los Pods y los ReplicaSets.Cuando describes el estado deseado en un objeto Deployment, el controlador del Deployment se encarga de cambiar el estado actual al estado deseado de forma controlada. Puedes definir Deployments para crear nuevos ReplicaSets, o eliminar Deployments existentes y adoptar todos sus recursos con nuevos Deployments.
3. **StatefulSet**: Un StatefulSet es el objeto de la API workload que se usa para gestionar aplicaciones con estado.
4. **DaemonSet**: Un DaemonSet garantiza que todos (o algunos) de los nodos ejecuten una copia de un Pod. Conforme se añade más nodos al clúster, nuevos Pods son añadidos a los mismos. Conforme se elimina nodos del clúster, dichos Pods se destruyen. Al eliminar un DaemonSet se limpian todos los Pods que han sido creados.
5. **Job**: Un Job crea uno o más Pods y se asegura de que un número específico de ellos termina de forma satisfactoria. Conforme los pods terminan satisfactoriamente, el Job realiza el seguimiento de las ejecuciones satisfactorias. Cuando se alcanza un número específico de ejecuciones satisfactorias, la tarea (esto es, el Job) se completa. Al eliminar un Job se eliminan los Pods que haya creado.

## Comandos Kubernetes

Algunos comandos comunes:

```bash
# Listar los Namespace disponibles
$ kubectl get ns
# Listar Pods disponibles en un Namespace
$ kubectl -n kube-system get pods
# Listar Pods disponibles en un Namespace con mas detalles
$ kubectl -n kube-system get pods -o wide
# Eliminar un pod
$ kubectl -n kube-system delete pod NAME_RUN_POD
```

Como detalle extra si existe algún problema con el archivo de configuración al node podemos ejecutar:

```bash
$ export KUBECONFIG=PATH_CONFIG_FILE
```

## Ejemplos de Archivos Yaml

**1.** Para nuestro primer ejemplo crearemos y aplicaremos un archivo básico:

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

Para ejecutar el contenido de los archivos **yaml** debemos conocer algunos comandos:

```bash
$ kubectl apply -f basic_pod.yaml
```

Para ingresar al contenedor dentro del **pod** podemos ejecutar:

```bash
$ kubectl exec -it fcch-nginx -- sh
```

Para eliminar los **pod** creados:

```bash
$ kubectl delete pod fcch-nginx
# Para verificar 
$ kubectl get pods
```

**2.** Para este ejemplo utilizaremos variables de entorno y asignaremos recursos.

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

Si deseamos verificar el archivo **yaml** que se ejecuto podemos ejecutar:

```bash
$ kubectl get pod fcch-nginx -o yaml
```

Si se necesita ver los detalles de un **pod**:

```bash
$ kubectl describe pod fcch-nginx
```

**3.** En este ejemplo utilizaremos volumenes, el tipo será **StatefulSet** y utlizaremos **replicas**.

Un detalle antes de continuar, **PVC**: Persistent Volume Clain.

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

Para trabajar con **Volumenes** podemos ejecutar los siguientes comandos:

```bash
# Listar volumenes
$ kubectl get pvc
# Mostrar detalles del PVC
$ kubectl describe pvc app-pvc-app-test-0
# Listar Pods de tipo StatefulSet
$ kubectl get statefulsets
# Listar Pods de tipo StatefulSet
$ kubectl get stss
# Eliminar Pod de tipo StatefulSet
$ kubectl delete sts app-test
# Eliminar PVC
$ kubectl delete pvc app-pvc-app-test-0
```

Algunos comandos que nos pueden servir:

```bash
# Listado completo
$ kubectl get all
# Listar servicios
$ kubectl get svc
# Detalle del servicio
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
