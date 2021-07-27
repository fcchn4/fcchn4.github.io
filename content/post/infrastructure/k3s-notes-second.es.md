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

    - *Controlador de nodo*: Responsable de notar y responder cuando los nodos se caen.
    - *Controlador de trabajo*: Busca objetos de trabajo que representan tareas únicas y luego crea pods para ejecutar esas tareas hasta su finalización.
    - *Controlador de puntos finales*: Completa el objeto de puntos finales (es decir, se une a servicios y pods).
    - *Controladores de cuentas de servicio y tokens*: Crea cuentas predeterminadas y tokens de acceso a la API para nuevos espacios de nombres.

5. **cloud-controller-manager**: Un componente del plano de control de Kubernetes que incorpora una lógica de control específica de la nube (se ejecuta en un solo proceso con *kube-controller-manager*).

    - *Controlador de nodo*: Para verificar el proveedor de la nube para determinar si un nodo se ha eliminado en la nube después de que deja de responder.
    - *Controlador de ruta*: Para configurar rutas en la infraestructura de nube subyacente.
    - Controlador de servicio*: Para crear, actualizar y eliminar equilibradores de carga de proveedores de nube.

6. **kubelet**: Un agente que se ejecuta en cada nodo del clúster, se asegura de que los contenedores se ejecuten en un Pod.
7. **kube-proxy**: Es un proxy de red que se ejecuta en cada nodo de su clúster, implementando parte del concepto de servicio de Kubernetes.
8. **container runtime**: El tiempo de ejecución del contenedor es el software responsable de ejecutar los contenedores.
9. **kube-scheduler**: Componente del plano de control que busca pods recién creados sin un nodo asignado y selecciona un nodo para que se ejecuten.

## Instalación Kubectl

Para poder trabajar con nuestro cluter **RPI** de [K3s](https://k3s.io/), podemos trabajar desde el **Server Node** o instalar [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/) en nuestra computadora personal, para este caso instalaremos el paquete para una distribución [GNU/Linux](https://www.gnu.org/home.es.html), [Debian Buster 10](https://debian.org) agregando el repositorio de la siguiente forma:

```bash
# Actualizar e instalar los paquetes necesarios
$ sudo apt-get update
$ sudo apt-get install -y apt-transport-https ca-certificates curl
# Descargamos la clave de firma pública
$ sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
# Agregar el repositorio oficial
$ echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
# Actualizar nuevamente e instalamos el paquete
$ sudo apt install update
$ sudo apt install -y kubectl
```

## Configuración de Kubectl con el Cluster K3s

Para trabajar desde nuestra computadora personal, tenemos que crear un archivo de configuración dentro de la carpeta **.kube** el archivo de configuración debe tener el nombre **config**, la carpeta y el archivo deben ser creados con los permisos adecuados:

```bash
$ mkdir ~/.kube
$ touch ~/.kube/config
$ chmod 775 ~/.kube
$ chmod 420 ~/.kube/config
```

El contenido del archivo **config** debe ser similar a este:

```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: CONTENIDO_DEL_CERTIFICADO
    server: https://IP_O_DOMINIO:6443
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
    client-certificate-data: CONTENIDO_DEL_CERTIFICADO
    client-key-data: CONTENIDO_DE_LA_CLAVE_DEL_CERTIFICADO
```

Estos datos se pueden obtener desde el **Server Node**, exactamente el dato se encuentra en **/etc/rancher/k3s/k3s.yaml**.

## Pruebas Después de la Instalación

Desde nuestra computadora local podemos ejecutar los siguientes comandos:

```bash
$ kubectl version
# Si existe algún problema con el anterior comando podemos ejecutar:
$ kubectl version --client=true
```

Si todo está correcto tendremos una salida similar a esta:

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

## Artículos K3s

1. [**K3s - Parte 1**](https://blog.fcch.xyz/post/infrastructure/k3s-notes-first/)
2. [**K3s - Parte 2**](https://blog.fcch.xyz/post/infrastructure/k3s-notes-second/)
3. [**K3s - Parte 3**](https://blog.fcch.xyz/post/infrastructure/k3s-notes-third/) - (en proceso)

## Referencias

- [**Inicio Rápido**](https://rancher.com/docs/k3s/latest/en/quick-start/)
- [**Kube Dashboard**](https://rancher.com/docs/k3s/latest/en/installation/kube-dashboard/)
- [**Docs K3S**](https://rancher.com/docs/)
- [**Conceptos Kubernetes**](https://kubernetes.io/docs/concepts/_print/)
- [**Docs Kubernetes**](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
- [**etcd**](https://etcd.io/)
- [**Cluster Admin Access**](https://rancher.com/docs/rancher/v2.x/en/cluster-admin/cluster-access/kubectl/)
- [**Cluster Accesss**](https://rancher.com/docs/k3s/latest/en/cluster-access/)