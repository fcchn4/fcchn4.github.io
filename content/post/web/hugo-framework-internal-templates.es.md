+++
title = "Hugo Framework - Templates Internos"
author = "Fcch"
date = "2022-01-07"
description = "Guías Web"
featured = true
tags = [
    "git",
    "hugo"
]
categories = [
    "web",
]
series = ["Hugo"]
thumbnail = "images/hugo-framework-internal-templates/hugo-article-image.png"
+++

Crear sitios web con [**Hugo Framework**](https://gohugo.io/) hizo que explore mas sobre esta herramienta tan útil. Una funcionalidad necesaria importante fue agregar a un proyecto las propiedades de [**The Open Graph Protocol**](https://ogp.me/), la primera idea fue editar los archivos HTML del **theme** y agregar las etiquetas correspondientes, pero luego de consultar la documentación oficial encontramos la solución mas adecuada en esta [**sección**](https://gohugo.io/templates/internal/).

<!--more-->

![](/images/hugo-framework-internal-templates/hugo-post-image.png)

Existen diferentes templates internos, cada template tiene los ejemplos para el archivo **config** en los formatos disponibles **yaml**, **toml** y **json**.

## Templates Disponibles

Estos son los templates internos que puedes utilizar en tus proyectos:

- **disqus** ➜ _internal/disqus.html
- **google analytics** ➜ _internal/google_analytics.html
- **google analytics async** ➜ _internal/google_analytics_async.html
- **open graph** ➜ _internal/opengraph.html
- **pagination** ➜ _internal/pagination.html
- **schema** ➜ _internal/schema.html
- **twitter cards** ➜ _internal/twitter_cards.html

## Ejemplo de uso con Open Graph

Para este ejemplo tenemos el archivo **config.yaml** con los datos:

```yml
params:
  description: Texto de descripción
  images:
  - site-feature-image.jpg
  title: Titulo de la publicación
```

Para agregar el template podemos editar el **theme**, en el ejemplo el theme tiene como ruta **theme/name_theme/layouts/index.html**.

```cmd
<head>
...
{{ template "_internal/opengraph.html" . }}
...
</head>
```

El resultado es similar a esto:

![](/images/hugo-framework-internal-templates/hugo-opengraph-es.png)

## Referencias

- [**Open Graph**](https://ogp.me/)
- [**Hugo Documentación**](https://gohugo.io/templates/internal/)
