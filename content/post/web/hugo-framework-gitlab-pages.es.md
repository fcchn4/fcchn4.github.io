+++
title = "Hugo Framework y Gitlab Pages"
author = "Fcch"
date = "2021-01-20"
description = "Guías Web"
featured = true
tags = [
    "git",
    "gitlab"
]
categories = [
    "web",
]
series = ["Hugo"]
thumbnail = "images/hugo-gitlab/hugo-gitlab.png"
+++

Una de las primeras ideas para tener un sitio web o blog es utilizar algún [**CMS**](https://es.wikipedia.org/wiki/Sistema_de_gesti%C3%B3n_de_contenidos) como [**Wordpress**](https://es.wordpress.org/), [**Drupal**](https://www.drupal.org/) o el buen [**SPIP**](https://www.spip.net/es_rubrique23.html), todos estos desarrollados con [**PHP**](https://www.php.net) y requieren de una base de datos para funcionar, un servidor o hosting.

<!--more-->

![](/images/hugo-gitlab/hugo-gitlab.png)

Una de las soluciones que existen para evitar comprar un hosting o servidor, es generar sitios estáticos con alguna herramienta como [**Jekyll**](https://jekyllrb.com/) o mi preferido [**Hugo Framework**](https://gohugo.io/), estos combinados con [**Gitlab Pages**](https://about.gitlab.com/stages-devops-lifecycle/pages/) o [**Github Pages**](https://pages.github.com/).

Ya hicimos un post para crear un blog con [**Hugo Framework + Github Pages**](https://blog.fcch.xyz/posts/2020/01/blog-con-hugo-framework-y-github-pages/), en esta oportunidad haremos un pequeño demo para utilizar **Hugo Framework + Gitlab Pages**, utilizando el dominio por defecto como **USUARIO.gitlab.io** y luego personalizaremos el dominio a uno propio.

## Paso 1: Iniciamos el proyecto Hugo en local

Luego de instalar [**Hugo Framework**](https://github.com/gohugoio/hugo/releases), podemos utilizar el comando **hugo** desde una terminal para inicializar el proyecto.

```cmd
$ hugo new site usuario.gitlab.io --force
```

### - Preparamos el Sitio Web y archivos Git

```cmd
$ cd usuario.gitlab.io
$ git init
$ echo public > .gitignore
$ git submodule add https://github.com/dev_name/theme_name themes/theme_name
$ echo 'theme = "theme_name"' >> config.toml
$ hugo new posts/first-post.md
```

### - Requerimientos Gitlab Pages

Antes de subir el proyecto a un repositorio [**Gitlab**](https://gitlab.com), editamos el archivo **.gitmodules** para agregar URL relativas.

[**Gitlab**](https://gitlab.com) utiliza HTTPS para obtener código fuente en los **runner** compartidos, entonces:

```cmd
## Para un fork que pertenece al mismo grupo o usuario
$ vim .gitmodules
-- [submodule "themes/theme_name"]
-- path = themes/theme_name
-- url = ../../dev_name/theme_name.git ## Línea nueva

## Para un proyecto fuera del grupo o usuario
$ vim .gitmodules
-- [submodule "themes/theme_name"]
-- path = themes/theme_name
-- url = https://gitlab.com/dev_name/theme_name.git ## Línea nueva

## Actualizamos los submódulos
$ git submodule update
```

### - Gitlab CI

Creamos el archivo **.gitlab-ci.yml** para desplegar el proyecto al realizar push al repositorio, el contenido para este archivo seria el siguiente:

```cmd
$ vim .gitlab-ci.yml
-- image: registry.gitlab.com/pages/hugo:latest
-- 
-- variables:
--   GIT_SUBMODULE_STRATEGY: recursive
-- 
-- test:
--   script:
--   - hugo
--   except:
--   - master
-- 
-- pages:
--   script:
--   - hugo
--   artifacts:
--     paths:
--     - public
--   only:
--   - master
```

### - Repositorio Gitlab

Para crear el repositorio debemos hacerlo con el nombre **usuario.gitlab.io** donde ¨usuario¨ es el nombre de usuario de **Gitlab**, tambien se puede crear un grupo y utilizar el nombre del grupo para el nombre de repositorio **grupo.gitlab.io**.

Luego de crear el repositorio agreamos el código, luego de agregar el URL al proyecto:

```cmd
$ git remote add origin git@gitlab.com:usuario/usuario.gitlab.io.git
$ git add .
$ git commit -m 'Hugo gitlab page'
$ git push origin master
```

### - Verificamos el despliegue en el repositorio

Si todo salió bien deberiamos tener una salida similar a la imagen, **Repositorio -> CI/CD -> Pipelines**.

![](/images/hugo-gitlab/gitlab-ci-deploy.png)

Luego podemos verificar con la URL: **http://usuario.gitlab.io**

## Paso 2: Gitlab Dominio personalizado

Con lo realizado anteriormente y si no existió algún problema podemos personalizar el dominio a uno que elijamos nosotros, para el ejemplo utilizaremos el dominio **dominio.com** y **www.dominio.com**.

### - Creando Dominios

En **Gitlab** podemos ingresar al espacio **Repositorio -> Settings -> Papes** y agregar el dominio y subdominio elegidos:

- **Para dominio.com**

![](/images/hugo-gitlab/gitlab-domain-add.png)

- **Para www.dominio.com**

![](/images/hugo-gitlab/gitlab-domain-add-www.png)

Con estos dos pasos terminados tendremos el repositorio y el subdominio con el usuario listos para funcionar, en el caso del subdominio personal se deberia crear los registros necesario en el servidor DNS.

### - Registros DNS y Verificaciones Gitlab

Para el dominio raiz **dominio.com** se debe crear un registro **A** con los siguientes datos:

![](/images/hugo-gitlab/gitlab-dns-record-root.png)

El registro **A** para el dominio raiz **dominio.com** con el valor **35.185.44.232**, luego un registro **TXT** con los valores que facilita **Gitlab Pages**.

Luego de crear los registros podemos verificar desde **Gitlab**:

![](/images/hugo-gitlab/gitlab-domain.png)

Para el subdominio **www.dominio.com** se debe crear un registro **CNAME** con los valores que facilita **Gitlab Pages**.

![](/images/hugo-gitlab/gitlab-subdominio-cname.png)

El registro **CNAME** para el subdominio **www.dominio.com** con el valor "**usuario.gitlab.io.**", luego un registro **TXT** con los valores que facilita **Gitlab Pages**.

## Clonación del repositorio

```cmd
$ git clone --recurse-submodules git@gitlab.com:usuario/usuario.gitlab.io.git
```

## Eliminando submodulos

```cmd
$ git submodule deinit -f -- themes/theme_name
$ rm -rf .git/modules/themes/themes_name
$ git rm -f themes/themes_name
$ git submodule status
```

## Referencias

- [**Gitlab Stages DevOps**](https://about.gitlab.com/stages-devops-lifecycle/pages/)
- [**Gitlab Pages**](https://docs.gitlab.com/ee/user/project/pages/)
- [**Custom Domains SSL TLS Certification**](https://docs.gitlab.com/ee/user/project/pages/custom_domains_ssl_tls_certification/index.html)
- [**Gitlab Git Submodules**](https://docs.gitlab.com/ee/ci/git_submodules.html)
- [**GitLab Pages examples**](https://gitlab.com/pages)