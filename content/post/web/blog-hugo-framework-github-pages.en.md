+++
title = "Blog with Hugo Framework and Github Pages"
author = "Fcch"
date = "2020-01-11"
description = "Web Guide"
featured = true
tags = [
    "hugo",
    "git",
    "github"
]
categories = [
    "web",
    "hugo",
]
series = ["Web Guide"]
aliases = ["web"]
thumbnail = "images/hugo-github/logo-hugo-github-400.png"
+++
Desde hace un tiempo estuve intentando crear un blog personal, la primera idea fue usar algún [**CMS**](https://es.wikipedia.org/wiki/Sistema_de_gesti%C3%B3n_de_contenidos) conocido como [**Wordpress**](https://es.wordpress.org/) o [**Drupal**](https://www.drupal.org/) pero ninguno llego a gustarme, luego use [**SPIP**](https://www.spip.net/es_rubrique23.html) por un tiempo donde pude aprender mucho de este [**CMS**](https://es.wikipedia.org/wiki/Sistema_de_gesti%C3%B3n_de_contenidos) Francés, pero mantener un sitio de solo publicaciones con texto y algunas imágenes no era el mas apropiado.

<!--more-->

![](/images/hugo-github/logo-hugo-github-400.png)

Luego estuve viendo los blogs de amigos y el que me llamo la atención fue de [**donkeysharp**](https://blog.donkeysharp.xyz/) donde se utiliza [**Hugo Framework**](https://gohugo.io/), un generador páginas estáticas a partir de plantillas predeterminadas, también utiliza [**Github Pages**](https://pages.github.com/) que te permite crear un sitio estático en un subdominio del tipo **usuario.github.io**, el mismo también te permite crear un sitio con un dominio o subdominio personal de la forma **www.dominio.info** o también **blog.dominio.info**, con esta información me puse a trabajar en el nuevo blog.

## Github y Github Pages 

El primer paso fue crear un nuevo repositorio en Github, algunas guías recomiendan que el repositorio tenga el nombre de la forma **usuario.github.io**, en mi caso solo utilice un subdomino personal.

- **Creando el Repositorio Nuevo**

![](/images/hugo-github/github-repo.png)

- **Configurando el Repositorio**

![](/images/hugo-github/github-page.png)

Con estos dos pasos terminados tendremos el repositorio y el subdominio con el usuario listos para funcionar, en el caso del subdominio personal se deberia crear los registros necesario en el servidor DNS.

## Configuración DNS Bind9

Muchos utilizan servidores DNS de los proveedores como [**Digital Ocean**](https://www.digitalocean.com/) o [**Linode**](https://www.linode.com/) para administrar su dominio, en el caso de utilizar Bind9 se tiene que crear los siguientes registros:

![](/images/hugo-github/bind9-subdomain.png)

Puede verificar los nuevos registros con el siguiente comando:

```cmd
    $ dig subdominio.dominio.info +nostats +nocomments +nocmd
```

## Iniciamos el Sitio con Hugo

Luego de crear el repositorio en Github, debemos clonarlo, ingresar al repositorio y crear una nueva rama de desarrollo:

```cmd
$ git clone URL/repositorio
$ cd hugo-static-site
$ git checkout -b development
$ echo public > .gitignore
$ git push origin development
```

Luego se debe crear la rama maestra que será independiente de la rama de  desarrollo, la maestra se usará para publicar el sitio.

```cmd
$ git checkout --orphan master
$ git reset --hard
$ echo '<h1>Hello World</h1>' > index.html
$ git commit -am "First index.html"
$ git push origin master
$ git checkout development
```

Con las ramas creadas y clasificadas podemos iniciar con crear las plantillas con [**Hugo**](https://gohugo.io/).

```cmd
$ cd ..
$ hugo new site hugo-static-site --force
$ cd hugo-static-site
```

También se debe hacer que el directorio **public** haga referencia a la rama **master**.

```cmd
$ git worktree add -B master public origin/master
```

Para inicializar el nuevo sitio instalamos un tema de nuestra preferencia, para el ejemplo utilizaremos el tema [**goa**](https://themes.gohugo.io/hugo-goa/).

```cmd
$ git submodule add https://github.com/shenoybr/hugo-goa themes/goa
```

Agregamos el nueva tema al archivo **config.toml**.

```cmd
$ echo 'theme = "goa"' >> config.toml
```

Creamos el nuevo y primer post.

```cmd
$ hugo new posts/first-post.md
```

Y asegúrese de que en el archivo creado esté marcado como **draft: false** para que pueda publicarse.

Con todo lo anterior podemos hacer una previsualización de todo lo trabajado con el servidor web que proporciona [**Hugo**](https://gohugo.io/).

```cmd
$ hugo server --watch -D
```

Cuando verificamos que todo este bien, iniciamos la generación del sitio.

```cmd
$ hugo -D 
```

El comando anterior creara los archivos estáticos en el directorio **public**. 

Con los archivos estáticos generados, subimos los cambios realizados en [**Hugo**](https://gohugo.io/) a la rama **development**.

```cmd
$ git add .
$ git commit -m 'Initialized hugo site'
$ git push origin development
```

Como el directorio **public** apunta a otra rama: **master**, debemos subir los archivos estáticos también.

```cmd
$ cd public
$ git add .
$ git commit -m 'publishing first-post'
$ git push origin master
```

Con estos ultimos pasos deberiamos tener un sitio web funcionando en el subdominio configurado en Github Pages o con el subdominio personal.

## Clonación del repositorio

```cmd
$ git clone -b nombre_branch --recurse-submodules URL/repositorio
$ cd repositorio
$ mkdir public
$ git worktree add -B master public origin/master
```

## Eliminando submodulos

```cmd
$ git submodule deinit -f -- ruta/nombre_submodulo
$ rm -rf .git/modules/ruta/nombre_submodulo
$ git rm -f ruta/submodulo
$ git submodule status
```

## Referencias

- [**Donkeysharp**](https://blog.donkeysharp.xyz/)
- [**Hosting on Github**](https://gohugo.io/hosting-and-deployment/hosting-on-github/)
- [**Hugo Quick Start**](https://gohugo.io/getting-started/quick-start/)