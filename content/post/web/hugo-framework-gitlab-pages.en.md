+++
title = "Hugo Framework and Gitlab Pages"
author = "Fcch"
date = "2021-01-20"
description = "Web Guide"
featured = true
tags = [
    "hugo",
    "git",
    "gitlab"
]
categories = [
    "web",
    "hugo",
]
series = ["Web"]
thumbnail = "images/hugo-gitlab/hugo-gitlab.png"
+++

One of the first ideas to have a website or blog is to use a [**CMS**](https://es.wikipedia.org/wiki/Sistema_de_gesti%C3%B3n_de_contenidos) like [**Wordpress**](https://es.wordpress.org/), [**Drupal**](https://www.drupal.org/) or the good [**SPIP**](https://www.spip.net/es_rubrique23.html), all of these developed with [**PHP**](https://www.php.net) and require a database to function, a server or hosting.

<!--more-->

![](/images/hugo-gitlab/hugo-gitlab.png)

One of the solutions that exist to avoid buying a hosting or server is to generate static sites with a tool like [**Jekyll**](https://jekyllrb.com/) or my favorite [**Hugo Framework**](https://gohugo.io/), these combined with [**Gitlab Pages**](https://about.gitlab.com/stages-devops-lifecycle/pages/) or [**Github Pages**](https://pages.github.com/).

We already made a post to create a blog with [**Hugo Framework + Github Pages**](https://blog.fcch.xyz/posts/2020/01/blog-con-hugo-framework-y-github-pages/), this time we will make a small demo to use **Hugo Framework + Gitlab Pages**, using the default domain as **USUARIO.gitlab.io** and then we will customize the domain to our own.

## Step 1: We started the Hugo Project Locally

After installing [**Hugo Framework**](https://github.com/gohugoio/hugo/releases), we can use the **hugo** command from a terminal to initialize the project.

```cmd
$ hugo new site usuario.gitlab.io --force
```

### - We prepare the Website and Git files

```cmd
$ cd usuario.gitlab.io
$ git init
$ echo public > .gitignore
$ git submodule add https://github.com/dev_name/theme_name themes/theme_name
$ echo 'theme = "theme_name"' >> config.toml
$ hugo new posts/first-post.md
```

### - Gitlab Pages Requirements

Before uploading the project to a [**Gitlab**](https://gitlab.com) repository, we edit the **.gitmodules** file to add relative URLs.

[**Gitlab**](https://gitlab.com) uses HTTPS to get source code on shared **runners**, so:

```cmd
## For a fork that belongs to the same group or user
$ vim .gitmodules
-- [submodule "themes/theme_name"]
-- path = themes/theme_name
-- url = ../../dev_name/theme_name.git ## New line

## For a project outside the group or user
$ vim .gitmodules
-- [submodule "themes/theme_name"]
-- path = themes/theme_name
-- url = https://gitlab.com/dev_name/theme_name.git ## New line

## Update submodules
$ git submodule update
```

### - Gitlab CI

We create the **.gitlab-ci.yml** file to deploy the project when pushing to the repository, the content for this file would be the following:

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

### - Gitlab Repository

To create the repository we must do it with the name **usuario.gitlab.io** where ¨user¨ is the **Gitlab** username, you can also create a group and use the group name for the name of repository **grupo.gitlab.io**.

After creating the repository we add the code, after adding the URL to the project:

```cmd
$ git remote add origin git@gitlab.com:usuario/usuario.gitlab.io.git
$ git add .
$ git commit -m 'Hugo gitlab page'
$ git push origin master
```

### - We verify the deployment in the repository

If everything went well we should have an output similar to the image, **Repositorio -> CI/CD -> Pipelines**.

![](/images/hugo-gitlab/gitlab-ci-deploy.png)

Then we can check with the URL: **http://usuario.gitlab.io**

## Step 2: Gitlab Custom Domain

With what has been done previously and if there was no problem we can customize the domain to one of our own choosing, for the example we will use the domain **domain.com** and **www.domain.com**.

### - Creating Domains

In **Gitlab** we can enter the **Repository -> Settings -> Papes** space and add the chosen domain and subdomain:

- **For dominio.com**

![](/images/hugo-gitlab/gitlab-domain-add.png)

- **For www.dominio.com**

![](/images/hugo-gitlab/gitlab-domain-add-www.png)

With these two steps completed we will have the repository and the subdomain with the user ready to work, in the case of the personal subdomain, the necessary records should be created in the DNS server.

### - DNS Records and Gitlab Checks

For the root domain **domain.com** an **A** record must be created with the following data:

![](/images/hugo-gitlab/gitlab-dns-record-root.png)

The **A** record for the root domain **domain.com** with the value **35.185.44.232**, then a **TXT** record with the values provided by **Gitlab Pages**.

After creating the records we can verify from **Gitlab**:

![](/images/hugo-gitlab/gitlab-domain.png)

For the subdomain **www.dominio.com** a record **CNAME** must be created with the values provided by **Gitlab Pages**.

![](/images/hugo-gitlab/gitlab-subdominio-cname.png)

The **CNAME** record for the subdomain **www.domain.com** with the value "**usuario.gitlab.io.**", then a **TXT** record with the values provided **Gitlab Pages**.

## Clone repository

```cmd
$ git clone --recurse-submodules git@gitlab.com:usuario/usuario.gitlab.io.git
```

## Delete submodules

```cmd
$ git submodule deinit -f -- themes/theme_name
$ rm -rf .git/modules/themes/themes_name
$ git rm -f themes/themes_name
$ git submodule status
```

## References

- [**Gitlab Stages DevOps**](https://about.gitlab.com/stages-devops-lifecycle/pages/)
- [**Gitlab Pages**](https://docs.gitlab.com/ee/user/project/pages/)
- [**Custom Domains SSL TLS Certification**](https://docs.gitlab.com/ee/user/project/pages/custom_domains_ssl_tls_certification/index.html)
- [**Gitlab Git Submodules**](https://docs.gitlab.com/ee/ci/git_submodules.html)
- [**GitLab Pages examples**](https://gitlab.com/pages)