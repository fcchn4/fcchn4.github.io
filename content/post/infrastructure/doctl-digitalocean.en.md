+++
title = "Doctl Cli - Digitalocean"
author = "Fcch"
date = "2021-11-02"
description = "Servers and Infrastructure Guide"
featured = false
tags = [
    "cloud",
    "digitalocean",
    "doctl"
]
categories = [
    "Infrastructure",
]
series = ["Servers"]
thumbnail = "images/doctl-digitalocean/doctl.png"
+++

**[Digitalocean](https://digitalocean.com)** also has a CLI to manage the "**[DOCTL](https://www.digitalocean.com/docs/apis-clis/doctl/reference/)**" infrastructure, which works similarly to **[AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)**, this tool allows us to avoid using the web interface of this provider.

<!--more-->

![](/images/doctl-digitalocean/doctl.png)

## Install Binary Tools

The first thing I have to do is download and install the binary, this for the current version **1.54.0**:

```cmd
$ cd ~
$ wget https://github.com/digitalocean/doctl/releases/download/v1.54.0/doctl-1.54.0-linux-amd64.tar.gz
$ tar xf ~/doctl-1.54.0-linux-amd64.tar.gz
$ sudo mv ~/doctl /usr/local/bin
```

## Create Token API

Before using "**[doctl](https://www.digitalocean.com/docs/apis-clis/doctl/reference/)**", you need to create a **[digitalocean](https://digitalocean.com)** API Token for your account, with read and write access from the **Applications** and **API** page in the control panel.

![](/images/doctl-digitalocean/token-api-name.png)

The token string is only shown once, so please copy and keep in a safe place.

![](/images/doctl-digitalocean/token-api-value.png)

## Account Access with Token API and doctl

The Token API grants access to "**[doctl](https://www.digitalocean.com/docs/apis-clis/doctl/reference/)**" for your **[digitalocean](https://digitalocean.com)** account. Pass the Token string when prompted, when executing the "**[doctl](https://www.digitalocean.com/docs/apis-clis/doctl/reference/)**" command.

You can use multiple accounts, using different names, for this function the **--context** parameter allows it.

```cmd
$ doctl auth init --context <NOMBRE_CUENTA1>
$ doctl auth init --context <NOMBRE_CUENTA2>
$ doctl auth init --context <NOMBRE_CUENTA3>
```

All accounts can be listed with the following command:

```cmd
$ doctl auth list
```

To switch between the different existing accounts:

```cmd
$ doctl auth switch --context <NOMBRE_CUENTA_DISPONIBLE>
```

## Examples of use

We can use "**[doctl](https://www.digitalocean.com/docs/apis-clis/doctl/reference/)**" in different cases.

- **Account verify** 

```cmd
$ doctl account get
```

- **List Droplets**

```cmd
$ doctl compute droplet list
```

- **Create Droplet**

```cmd
$ doctl compute droplet create --region sfo2 --image ubuntu-18-04-x64 --size s-1vcpu-1gb <NOMBRE_DROPLET>
```

- **List Available Droplet Images**

```cmd
$ doctl compute image list --public | grep -i 'debian'
```

- **List Available Droplet Sizes**

```cmd
$ doctl compute size list
```

- **Format DOCTL Output**

```cmd 
$ doctl compute droplet list --format "ID,Name,PublicIPv4"
```

- **Create a Droplet with more details**

```cmd
$ doctl compute droplet create --region <REGION_CODE> \
  --image <IMAGE_CODE> --size <SIZE_NAME> \
  --enable-monitoring true --enable-private-networking true \
  --tag-name <TAG> --ssh-keys <FINGERPRINT>
```

- **Delete Droplet**

```cmd
$ doctl compute droplet delete <ID_DROPLET>
```

## References

- [**APIS CLIS**](https://www.digitalocean.com/docs/apis-clis/)
- [**DOCTL CLI**](https://www.digitalocean.com/docs/apis-clis/doctl/)

## DigitalOcean

| [![](https://web-platforms.sfo2.cdn.digitaloceanspaces.com/WWW/Badge%201.svg)](https://www.digitalocean.com/?refcode=f3953badd3d5&utm_campaign=Referral_Invite&utm_medium=Referral_Program&utm_source=badge) | [![](https://web-platforms.sfo2.digitaloceanspaces.com/WWW/Badge%203.svg)](https://www.digitalocean.com/?refcode=f3953badd3d5&utm_campaign=Referral_Invite&utm_medium=Referral_Program&utm_source=badge) | [![](https://web-platforms.sfo2.digitaloceanspaces.com/WWW/Badge%202.svg)](https://www.digitalocean.com/?refcode=f3953badd3d5&utm_campaign=Referral_Invite&utm_medium=Referral_Program&utm_source=badge) |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
