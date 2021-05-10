+++
title = "SPIP, Another Way to Install"
author = "Fcch"
date = "2020-01-06"
description = "Web Guide"
featured = true
tags = [
    "web",
    "spip",
    "cms"
]
categories = [
    "web",
    "spip",
]
series = ["Web"]
thumbnail = "images/spip-svn/logo-spip-400.png"
+++

[SPIP](https://www.spip.net/) is a French [CMS](https://es.wikipedia.org/wiki/Sistema_de_gesti%C3%B3n_de_contenidos) with simple installation, you do not need PHP and MySQL knowledge to proceed with the installation, it contains a web configuration menu and a simple administration space.

<!--more-->

![](/images/spip-svn/logo-spip-400.png)

There are different installation methods on the [official site](https://www.spip.net/es_download), you can download the Zip file to unzip and prepare the installation, another way that exists is to start the **Automatic Installation** which consists of downloading the **spip_loader.php** file and running it from a web browser.

The method that is not used very often is to download from an official [SPIP](https://www.spip.net/) repository using the old and reliable Subversion (SVN), this installation method is the one that is commonly used to be able to perform updates automatically in a single line command.

## Download SPIP

We start the download assuming that you already have a web server on the local machine or with a provider of your choice.

To start the download of SPIP, to have the latest stable version 3.2.7 we must download the branch [**spip-3.2**](https://www.spip.net/es_download) executing:

```cmd
$ mkdir spip-core
$ cd spip-core
$ svn checkout svn://trac.rezo.net/spip/branches/spip-3.2 .
```

After finishing the download we will have a file structure similar to this:

![](/images/spip-svn/spip-tree.png)

- For CMS updates, just run:

```cmd
$ svn upgrade
$ svn update 
```

- To make the customizations in SPIP without risk of losing the information after the updates, the following directories must be created:

```cmd
$ mkdir -p plugins/auto
$ mkdir squelettes
```

- To start the installation of SPIP we need to have the **IMG**, **tmp**, **local** and **config** directories with **777** permissions and if possible with the user and group **www-data**:

```cmd
$ chown -R www-data:www-data IMG tmp local config
$ chmod 777 IMG tmp local config
```

The directory structure should be as follows:

![](/images/spip-svn/spip-tree-complete.png)

## Install SPIP

With all the preparations ready we can start the installation of [**SPIP**](https://www.spip.net/).

To start the installation of [**SPIP**](https://www.spip.net/) we open the web browser of our preference and depending on the case we enter the URL:

- http://localhost/ecrire
- https://dominio.com/ecrire
- http://IP-server/ecrire

this depends on the configuration of the web server, if you are working locally or from a service provider.

- 1. When we enter the URL we have the SPIP welcome, where you can choose the installation language:

![](/images/spip-svn/spip-demo-01.png)

- 2. Then we have the menu for the connection to the database, in this case two options are given, the first is **MySQL** where the username and password must be entered.

![](/images/spip-svn/spip-demo-02.png)

For the example we will use the SQLite3 database for a quick installation.

![](/images/spip-svn/spip-demo-03.png)

- 3. Now a database with [**SPIP**](https://www.spip.net/) name and prefix is created, this in the case of no existing databases.

![](/images/spip-svn/spip-demo-04.png)

- 4. After creating the database, you must now enter the data for the user who will manage the CMS.

![](/images/spip-svn/spip-demo-05.png)

- 5. Finally, the installation is displayed, listing the plugins that are part of the CMS.

![](/images/spip-svn/spip-demo-06.png)

- 6. If everything went well, we should be able to enter the Administration area **private area**.

![](/images/spip-svn/spip-demo-07.png)

## References

- [**SPIP Official Website**](https://www.spip.net/en_rubrique25.html)
- [**SPIP Programmer**](https://programmer.spip.net/)
- [**SPIP Contrib**](https://contrib.spip.net/)
- [**SPIP Blog**](https://blog.spip.net/?lang=fr)