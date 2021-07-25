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

Continuamos con las pruebas en nuestro pequeño cluster con [**K3s**](https://k3s.io/) armado en las [**Raspberry Pi (RPI v3, RPI v4)**](https://www.raspberrypi.org/) que tengo en mi laboratorio, para las pruebas utlizamos la documentación oficial de [**K3s - Rancher**](https://rancher.com/docs/k3s/latest/en/) y también la documentación oficial de [**Kubernetes**](https://kubernetes.io/docs/tutorials/kubernetes-basics/).

<!--more-->

![](/images/k3s-kubernetes/k3s-rpi-v3-v4.jpg)

En la imagen tenemos como detalle:

1. **Tres Raspberry Pi 4, Modelo B** (1 Server Node y 2 Worker Nodes).
2. **Dos Raspberry Pi 3, Modelo B** (2 Worker Nodes, ).
3. Un **Switch Desktop Gigabit Tp-Link TL-SG1008D** de 8 puertos.

## Conceptos Básicos

Con los componentes de Kubernetes:

![](/images/k3s-kubernetes/components-of-kubernetes.png)

Podemos describir algunos componentes importantes: 

1. **kube-apiserver**: El servidor de API es un componente del plano de control de Kubernetes que expone la API de Kubernetes. El servidor de API es la interfaz del plano de control de Kubernetes.
2. **etcd**: Base de datos de tipo *clave-valor* consistente y de alta disponibilidad utilizado como almacén de respaldo de Kubernetes para todos los datos del clúster.
3. **kube-scheduler**: Componente del plano de control que busca pods recién creados sin un nodo asignado y selecciona un nodo para que se ejecuten.
4. **kube-controller-manager**: Componente del plano de control que ejecuta los procesos del controlador.
    - *Controlador de nodo*: responsable de notar y responder cuando los nodos se caen.
    - *Controlador de trabajo*: Busca objetos de trabajo que representan tareas únicas y luego crea pods para ejecutar esas tareas hasta su finalización.
    - *Controlador de puntos finales*: Completa el objeto de puntos finales (es decir, se une a servicios y pods).
    - *Controladores de cuentas de servicio y tokens*: Crea cuentas predeterminadas y tokens de acceso a la API para nuevos espacios de nombres.
5. **cloud-controller-manager**: Un componente del plano de control de Kubernetes que incorpora una lógica de control específica de la nube (se ejecuta en un solo proceso con *kube-controller-manager*).

    - *Controlador de nodo*: para verificar el proveedor de la nube para determinar si un nodo se ha eliminado en la nube después de que deja de responder
    - *Controlador de ruta*: para configurar rutas en la infraestructura de nube subyacente
    - Controlador de servicio*: para crear, actualizar y eliminar equilibradores de carga de proveedores de nube
6. **kubelet**: Un agente que se ejecuta en cada nodo del clúster. Se asegura de que los contenedores se ejecuten en un Pod.

7. **kube-proxy**: Es un proxy de red que se ejecuta en cada nodo de su clúster, implementando parte del concepto de servicio de Kubernetes.
8. **container runtime**: El tiempo de ejecución del contenedor es el software responsable de ejecutar los contenedores.

## Configuraciones Previas

## Instalación del Server node

## Instalación de Worker Nodes

## Operaciones Básicas

## Referencias

- [**Inicio Rapido**](https://rancher.com/docs/k3s/latest/en/quick-start/)
- [**Kube Dashboard**](https://rancher.com/docs/k3s/latest/en/installation/kube-dashboard/)
- [**Docs K3S**](https://rancher.com/docs/)
- [**Conceptos Kubernetes**](https://kubernetes.io/docs/concepts/_print/)
- [**Docs Kubernetes**](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
- [**etcd**](https://etcd.io/)