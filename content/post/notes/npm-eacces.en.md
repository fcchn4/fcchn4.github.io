+++
title = "NPM EACCES"
author = "Fcch"
date = "2020-08-12"
description = "Servers and Infrastructure Guide"
featured = true
tags = [
    "nodejs",
    "npm",
    "bash"
]
categories = [
    "Infrastructure",
    "Servers",
]
series = ["Servers"]
thumbnail = "images/aws-learning-path/fcch-route.png"
+++

I had to test the implementation of a **NodeJS** application where I needed to install global packages, the installation of global **NodeJS** packages in the operating system does not seem to be a good practice, so we found a solution to this problem.

<!--more-->

![](/images/npm-eacces/npm-error-img.png)

When running the command:

```cmd
$ npm install -g <NOMBRE_PAQUETE>
```

The problem is for permissions with **npm ERR! code EACCES** and to solve the problem immediately it was to execute it with **sudo**, this practice is common but it's not a good practice.

Searching I found that the solution to avoid the sudo command is to manually change the default **NPM** directory.

## Create the New Directory

We create a new folder where the new programs will be installed:

```cmd
$ mkdir ~/.npm-global
```

## Configure NPM for the New Directory Path

With the [**npm**](https://nodejs.org/en/) command we change the path of the package installation directory:

```cmd
$ npm config set prefix '~/.npm-global'
```

## Environment Variables

In some cases, there are guides that suggest you create a **.profile** file to create the variable, in this example we add the variable to the **.bashrc** file:

```cmd
$ echo 'NPM_CONFIG_PREFIX=~/.npm-global' >> ~/.bashrc
```

## Update Variables

We update the variables with the following command:

```cmd
$ source ~/.bashrc
```

## We test the Installation without sudo

To test the new functionality we execute the same command without prepending **sudo**:

```cmd
$ npm install -g <NOMBRE_PAQUETE>
```

## References

- [**NodeJS**](https://nodejs.org/en/docs/guides/)
- [**NodeJs Errors**](https://nodejs.org/api/errors.html)