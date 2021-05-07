+++
title = "Doctl Cli - Digitalocean"
author = "Fcch"
date = "2020-12-30"
description = "Guía Servidores e Infraestructura"
featured = true
tags = [
    "bash",
    "cloud",
    "digitalocean",
    "doctl"
]
categories = [
    "Infraestructura",
    "Servidores",
]
series = ["Guía Servidores"]
thumbnail = "images/doctl-digitalocean/doctl.png"
+++
**[Digitalocean](https://digitalocean.com)** también dispone de un CLI para manejar la infraestructura "**[DOCTL](https://www.digitalocean.com/docs/apis-clis/doctl/reference/)**", que tiene un funcionamiento similar a **[AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)**, esta herramienta nos permite evitar utilizar la interfaz web de este proveedor.

<!--more-->

![](/images/doctl-digitalocean/doctl.png)

## Instalación del Binario

Lo primero que toco hacer es descarga e instalar el binario, esto para la versión actual **1.54.0**:

```cmd
  $ cd ~
  $ wget https://github.com/digitalocean/doctl/releases/download/v1.54.0/doctl-1.54.0-linux-amd64.tar.gz
  $ tar xf ~/doctl-1.54.0-linux-amd64.tar.gz
  $ sudo mv ~/doctl /usr/local/bin
```

## Crear un Token API

Antes de utilizar "**[doctl](https://www.digitalocean.com/docs/apis-clis/doctl/reference/)**", es necesario crear un Token API de **[digitalocean](https://digitalocean.com)** para su cuenta, con acceso de lectura y escritura desde la página **Aplicaciones** y **API** en el panel de control. 

![](/images/doctl-digitalocean/token-api-name.png)

La cadena del token solo se muestra una vez, entonces copie y guarde en un lugar seguro.

![](/images/doctl-digitalocean/token-api-value.png)

## Acceso a cuenta con Token API y doctl

El Token API otorga acceso a "**[doctl](https://www.digitalocean.com/docs/apis-clis/doctl/reference/)**" para su cuenta de **[digitalocean](https://digitalocean.com)**. Pase la cadena del Token cuando se solicite, al ejecutar el comando de "**[doctl](https://www.digitalocean.com/docs/apis-clis/doctl/reference/)**".

Puede utilizar varias cuentas, utilizando diferentes nombres, para esta función el parámetro **--context** lo permite.

```cmd
  $ doctl auth init --context <NOMBRE_CUENTA1>
  $ doctl auth init --context <NOMBRE_CUENTA2>
  $ doctl auth init --context <NOMBRE_CUENTA3>
```

Se puede listar todas las cuentas con el siguiente comando:

```cmd
  $ doctl auth list
```

Para cambiar entre las diferentes cuentas existentes:

```cmd
  $ doctl auth switch --context <NOMBRE_CUENTA_DISPONIBLE>
```

## Ejemplos de uso

Podemos utilizar "**[doctl](https://www.digitalocean.com/docs/apis-clis/doctl/reference/)**" en diferentes casos.

- **Verificar Cuenta** 

```cmd
  $ doctl account get
```

- **Listar Droplets**

```cmd
  $ doctl compute droplet list
```

- **Crear Droplet**

```cmd
  $ doctl compute droplet create --region sfo2 --image ubuntu-18-04-x64 --size s-1vcpu-1gb <NOMBRE_DROPLET>
```

- **Eliminar Droplet**

```cmd
  $ doctl compute droplet delete <ID_DROPLET>
```

## Referencias

- [**APIS CLIS**](https://www.digitalocean.com/docs/apis-clis/)
- [**DOCTL CLI**](https://www.digitalocean.com/docs/apis-clis/doctl/)