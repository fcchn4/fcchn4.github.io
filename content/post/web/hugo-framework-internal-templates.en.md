+++
title = "Hugo Framework - Internal Templates"
author = "Fcch"
date = "2022-01-07"
description = "Web Guide"
featured = true
tags = [
    "git",
    "gitlab"
]
categories = [
    "web",
]
series = ["Hugo"]
thumbnail = "images/hugo-framework-internal-templates/hugo-article-image.png"
+++

Building websites with [**Hugo Framework**](https://gohugo.io/) made me explore more about this very useful tool. An important necessary functionality was to add the properties of [**The Open Graph Protocol**](https://ogp.me/) to a project, the first idea was to edit the HTML files of the **theme** and add the corresponding tags, but after consulting the official documentation we found the most appropriate solution in this [**section**](https://gohugo.io/templates/internal/).

<!--more-->

![](/images/hugo-framework-internal-templates/hugo-post-image.png)

There are different internal templates, each template has the examples for the **config** file in the available formats **yaml**, **toml** and **json**.

## Available Templates

These are the internal templates that you can use in your projects:

- **disqus** ➜ _internal/disqus.html
- **google analytics** ➜ _internal/google_analytics.html
- **google analytics async** ➜ _internal/google_analytics_async.html
- **open graph** ➜ _internal/opengraph.html
- **pagination** ➜ _internal/pagination.html
- **schema** ➜ _internal/schema.html
- **twitter cards** ➜ _internal/twitter_cards.html

## Example of use with Open Graph

For this example we have the file **config.yaml** with the data:

```yml
params:
  description: Text of description
  images:
  - site-feature-image.jpg
  title: Title post
```

To add the template we can edit the **theme**, in the example the theme has the path **theme/name_theme/layouts/index.html**.

```cmd
<head>
...
{{ template "_internal/opengraph.html" . }}
...
</head>
```

The result is similar to this:

![](/images/hugo-framework-internal-templates/hugo-opengraph-en.png)

## References

- [**Open Graph**](https://ogp.me/)
- [**Hugo Documentation**](https://gohugo.io/templates/internal/)
