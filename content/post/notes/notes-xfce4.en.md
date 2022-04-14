+++
title = "Notes XFCE4"
author = "Fcch"
date = "2022-04-13"
description = "Notes for configurations in XFCE4."
featured = false
tags = [
    "xfce",
]
categories = [
    "Notes",
]
series = ["GNU-Linux"]
thumbnail = "images/notes-xfce/xfce-hero.jpg"
+++

I was changing hardware in my work environment, I changed the keyboard to an English language one, for this reason I was configuring [**XFCE**](https://xfce.org/), which is my preferred graphical environment.

<!--more-->

![](/images/notes-xfce/xfce-hero.jpg)

## Change Keyboard Language in XFCE4

The first thing was to change the language, therefore to get to the configuration menu: **Settings** > **Keyboard** > **Layout**, the configuration should be in the image.

![](/images/notes-xfce/xfce-keyboard.png)

This configuration did not take effect after a reboot, to solve this problem we edited the general configuration file in: **/etc/default/keyboard**.

![](/images/notes-xfce/xfce-general.png)

The configuration should look similar to the image.

Keyboard shortcut for special characters:

- **Alt Gr** + **'** + **(a, e, i, o, u)** = **á, é, í, ó, ú** **✓**
- **Alt Gr** + **Shift** + **~** + **n** = **ñ** **✓**

## Activate Super Key + Whisker Menu

To activate the applications menu in XFCE you need the plugin **whiskermenu**, to be able to associate it to the Left Super key (Windows Logo).

The configuration must be carried out in: **Settings** > **Keyboard** > **Application Shortcuts**.

![](/images/notes-xfce/xfce-menu.png)

```bash
> xfce4-popup-whiskermenu
```

## VPN Connection

Finally, the installation of **XFCE** for **Debian Bullseye** or **Ubuntu Focal** does not come with the OpenVPN utilities installed by default, so it is necessary to install the necessary packages.

```bash
$ sudo apt install -y openvpn network-manager-openvpn network-manager-openvpn-gnome
```

![](/images/notes-xfce/xfce-vpn.png)

## References

- [**XFCE**](https://xfce.org/)
- [**Compose Key**](https://help.ubuntu.com/community/ComposeKey)
