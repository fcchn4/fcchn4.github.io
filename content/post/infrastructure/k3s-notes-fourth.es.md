+++
title = "K3s Apuntes - Parte 4"
author = "Fcch"
date = "2021-07-28"
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
thumbnail = "images/k3s-kubernetes/k3s-kubernetes-part-4.png"
+++

Para terminar el pequeño laboratorio y finalizar la última parte de [**K3s**](https://k3s.io/), nombraremos algunos detalles importantes, instalaremos y crearemos el servicio para [**Kubernetes Dashboard**](https://rancher.com/docs/k3s/latest/en/installation/kube-dashboard/) y por último listaremos algunas herramientas interesantes.

<!--more-->

![](/images/k3s-kubernetes/k3s-rpi-part-4.jpg)

## Pod Networking

A cada Pod se le asigna una dirección IP única para cada familia de direcciones. Cada contenedor de un Pod comparte el espacio de nombres de la red, incluida la dirección IP y los puertos de red. Dentro de un Pod (y solo entonces), los contenedores que pertenecen al Pod pueden comunicarse entre sí usando localhost. Cuando los contenedores en un Pod se comunican con entidades fuera del Pod, deben coordinar cómo usan los recursos de red compartidos (como los puertos). Dentro de un Pod, los contenedores comparten una dirección IP y un espacio de puerto, y pueden encontrarse entre sí a través de localhost. Los contenedores de un Pod también pueden comunicarse entre sí mediante comunicaciones estándar entre procesos, como semáforos SystemV o memoria compartida POSIX. Los contenedores en diferentes Pods tienen direcciones IP distintas y no pueden comunicarse por IPC sin configuración especial. Los contenedores que desean interactuar con un contenedor que se ejecuta en un Pod diferente pueden usar redes IP para comunicarse.

**CNI - Cluster Networking Interface**: Es un marco de red que permite la configuración dinámica de recursos de red a través de un grupo de bibliotecas y especificaciones escritas por Go. La especificación mencionada para el complemento describe una interfaz que configuraría la red, aprovisionaría las direcciones IP y mantendría la conectividad de múltiples hosts.

En el contexto de Kubernetes, el CNI se integra a la perfección con el kubelet para permitir la configuración de red automática entre pods utilizando una red subyacente o superpuesta. Una red subyacente se define en el nivel físico de la capa de red compuesta por enrutadores y conmutadores.

**Calico**: Es una solución de seguridad de redes y redes de código abierto para contenedores, máquinas virtuales y cargas de trabajo nativas basadas en host. Calico admite varios planos de datos, incluidos: un plano de datos eBPF puro de Linux, un plano de datos de red Linux estándar y un plano de datos HNS de Windows. Calico proporciona una pila de redes completa, pero también se puede utilizar junto con los CNI del proveedor de la nube para proporcionar la aplicación de políticas de red.

## Servicios Kubernetes

1. **Cluester IP**: Asigna una IP Fija dentro de un cluster, funciona con un pequeño Load Balancer.
2. **Node Port**: Similar al anterior, pero a nivel de puertos, crea un puerto en cada nodo recibe todo el tráfico y direcciona al servicio deseado.
3. **Load Balancer**: Crea un Balanceador de Carga en un proveedor de nube y redirecciona el tráfico de los Pods.
4. **Ingress**: Expone rutas HTTP y HTTPS desde fuera del clúster a servicios dentro del clúster, el enrutamiento del tráfico se controla mediante reglas definidas en el recurso Ingress.

## Ejemplo Simple

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

Algunos comandos útiles:

```bash
# Listar tipos Ingress
$ kubectl get ing
# Listar servicios
$ kubectl -n new-ingress get svc
```

## Dashboard Kubernetes

K3s nos facilita un forma simple de instalación para el Dashboard, solo se debe ejecutar los siguientes comandos:

```bash
GITHUB_URL=https://github.com/kubernetes/dashboard/releases
VERSION_KUBE_DASHBOARD=$(curl -w '%{url_effective}' -I -L -s -S ${GITHUB_URL}/latest -o /dev/null | sed -e 's|.*/||')
sudo k3s kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/${VERSION_KUBE_DASHBOARD}/aio/deploy/recommended.yaml
```

Se debe crear los archivos: 

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

Luego se debe ejecutar el comando para aplicar los archivos **yaml**.

```bash
$ sudo k3s kubectl create -f dashboard.admin-user.yml -f dashboard.admin-user-role.yml
```

Tenemos que obtener el token para poder ingresar al dashboard con el usuario **admin-user**:

```bash
$ sudo k3s kubectl -n kubernetes-dashboard describe secret admin-user-token | grep '^token'
```

Por último podemos exponer el servicio de manera local con:

```bash
$ sudo k3s kubectl proxy
```

Si se necesita editar el **Namespace** de **kubernetes-dashboard** podemos ejecutar:

```bash
$ kubectl -n kubernetes-dashboard edit service kubernetes-dashboard
$ kubectl -n kubernetes-dashboard get services
```

En el ejemplo el **Server Node** se encuentra sin entorno gráfico, y para el ejemplo se creó un tunel SSH para el puerto 8001:

```bash
$ ssh -L 8001:127.0.0.1:8001 -N -f -l USER IP-DOMAIN
```

Y para ingresar al **Dashboard** desde el navegador web la ruta seria:

```bash
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/
```

Si todo se ejecutó sin problemas nuestro **Dashboard** seria similar a esto:

- Lista de nodos

![](/images/k3s-kubernetes/kubernetes-dashboard-1.png)

- Lista de Namespaces

![](/images/k3s-kubernetes/kubernetes-dashboard-2.png)

- Configuración del Dashboard

![](/images/k3s-kubernetes/kubernetes-dashboard-3.png)

Comandos útiles:

```bash
$ kubectl config view
$ kubectl get services --all-namespaces
$ kubectl -n kubernetes-dashboard edit service kubernetes-dashboard
$ kubectl -n kubernetes-dashboard get svc
```

## Herramientas Interesantes

1. [**Lens**](https://k8slens.dev/): Es un sistema que toma el control de sus clústeres de Kubernetes.
2. [**Kind**](https://kind.sigs.k8s.io/): Es una herramienta para ejecutar clústeres de Kubernetes locales mediante los "nodos" de contenedor de Docker.
3. [**Minikube**](https://minikube.sigs.k8s.io/docs/start/): Es un Kubernetes local y se centra en facilitar el aprendizaje y el desarrollo para Kubernetes. 
4. [**Helm**](https://helm.sh/): Funciona como un gerente de empaquetación para Kubernetes, Helm es la mejor manera de buscar, compartir y utilizar software creado para Kubernetes.
5. [**Supervisord**](http://supervisord.org/): Es un sistema cliente/servidor que permite a sus usuarios monitorear y controlar una serie de procesos en sistemas operativos similares a UNIX (No es recomendable ejecutar dos procesos por contenedor, pero si es necesario podemos utilizar esta herramienta).

## Artículos K3s

1. [**K3s - Parte 1**](https://blog.fcch.xyz/post/infrastructure/k3s-notes-first/)
2. [**K3s - Parte 2**](https://blog.fcch.xyz/post/infrastructure/k3s-notes-second/)
3. [**K3s - Parte 3**](https://blog.fcch.xyz/post/infrastructure/k3s-notes-third/)
4. [**K3s - Parte 4**](https://blog.fcch.xyz/post/infrastructure/k3s-notes-fourth/)

## Referencias

- [**Inicio Rápido**](https://rancher.com/docs/k3s/latest/en/quick-start/)
- [**Kube Dashboard**](https://rancher.com/docs/k3s/latest/en/installation/kube-dashboard/)
- [**Docs K3S**](https://rancher.com/docs/)
- [**Conceptos Kubernetes**](https://kubernetes.io/es/docs/concepts/)
- [**Docs Kubernetes**](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
- [**Kubernetes Proxy**](https:kubernetes-dashboard:/proxy/)