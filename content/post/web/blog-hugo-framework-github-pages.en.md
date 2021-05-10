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
series = ["Web"]
aliases = ["web"]
thumbnail = "images/hugo-github/logo-hugo-github-400.png"
+++
For some time I was trying to create a personal blog, the first idea was to use a [**CMS**](https://es.wikipedia.org/wiki/Sistema_de_gesti%C3%B3n_de_contenidos) known as [**Wordpress**](https://es.wordpress.org/) or [**Drupal**](https://www.drupal.org/) but I didn't like any of them, then I used [**SPIP**](https://www.spip.net/es_rubrique23.html) for a while where I could learn a lot about this French [**CMS**](https://es.wikipedia.org/wiki/Sistema_de_gesti%C3%B3n_de_contenidos), but keep a site of only publications with text and some images was not the most appropriate.

<!--more-->

![](/images/hugo-github/logo-hugo-github-400.png)

Then I was looking at the blogs of friends and the one that caught my attention was [**donkeysharp**](https://blog.donkeysharp.xyz/) where [**Hugo Framework**](https://gohugo.io/) is used, a static page generator from default templates, it also uses [**Github Pages**](https://pages.github.com/) that allows you to create a static site in a user type **usuario.github.io**, it also allows you to create a site with a personal domain or subdomain in the form **www.dominio.info** or also **blog.dominio.info**, with this information I started to work on the new blog.

## Github and Github Pages 

The first step was to create a new repository on Github, some guides recommend that the repository has the name of the form **user.github.io**, in my case just use a personal sub-domain.

- **Creating the New Repository**

![](/images/hugo-github/github-repo.png)

- **Configuring the Repository**

![](/images/hugo-github/github-page.png)

With these two steps completed we will have the repository and the subdomain with the user ready to work, in the case of the personal subdomain, the necessary records should be created in the DNS server.

## Configuration DNS Bind9

Many use DNS servers from providers such as [**digitalocean**](https://www.digitalocean.com/) or [**linode**](https://www.linode.com/) to manage their domain, in the case of using Bind9 the following records must be created:

![](/images/hugo-github/bind9-subdomain.png)

You can check the new records with the following command:

```cmd
$ dig subdominio.dominio.info +nostats +nocomments +nocmd
```

## Start Hugo Website

After creating the repository on Github, we must clone it, enter the repository and create a new development branch:

```cmd
$ git clone URL/repositorio
$ cd hugo-static-site
$ git checkout -b development
$ echo public > .gitignore
$ git push origin development
```

Then the master branch must be created that will be independent from the development branch, the master branch will be used to publish the site.

```cmd
$ git checkout --orphan master
$ git reset --hard
$ echo '<h1>Hello World</h1>' > index.html
$ git commit -am "First index.html"
$ git push origin master
$ git checkout development
```

With the branches created and classified we can start with creating the templates with [**Hugo**](https://gohugo.io/).

```cmd
$ cd ..
$ hugo new site hugo-static-site --force
$ cd hugo-static-site
```

The **public** directory must also be made to reference the **master** branch.

```cmd
$ git worktree add -B master public origin/master
```

To initialize the new site we install a theme of our preference, for the example we will use the [**goa**](https://themes.gohugo.io/hugo-goa/) theme.

```cmd
$ git submodule add https://github.com/shenoybr/hugo-goa themes/goa
```

We add the new theme to the **config.toml** file.

```cmd
$ echo 'theme = "goa"' >> config.toml
```

We create the new and first post.

```cmd
$ hugo new posts/first-post.md
```

And make sure that in the created file it is marked as **draft: false** so that it can be published.

With all of the above we can make a preview of everything worked with the web server provided by [**Hugo**](https://gohugo.io/).

```cmd
$ hugo server --watch -D
```

When we verify that everything is fine, we start the generation of the site.

```cmd
$ hugo -D 
```

The above command creates the static files in the **public** directory.

With the static files generated, we upload the changes made to [**Hugo**](https://gohugo.io/) to the **development** branch.

```cmd
$ git add .
$ git commit -m 'Initialized hugo site'
$ git push origin development
```

Since the public directory points to another branch: **master**, we must upload the static files as well.

```cmd
$ cd public
$ git add .
$ git commit -m 'publishing first-post'
$ git push origin master
```

With these last steps we should have a website working in the subdomain configured in Github Pages or with the personal subdomain.

## Clone Repository

```cmd
$ git clone -b nombre_branch --recurse-submodules URL/repositorio
$ cd repositorio
$ mkdir public
$ git worktree add -B master public origin/master
```

## Delete Submodules

```cmd
$ git submodule deinit -f -- ruta/nombre_submodulo
$ rm -rf .git/modules/ruta/nombre_submodulo
$ git rm -f ruta/submodulo
$ git submodule status
```

## References

- [**Donkeysharp**](https://blog.donkeysharp.xyz/)
- [**Hosting on Github**](https://gohugo.io/hosting-and-deployment/hosting-on-github/)
- [**Hugo Quick Start**](https://gohugo.io/getting-started/quick-start/)