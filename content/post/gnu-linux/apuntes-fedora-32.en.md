+++
title = "Notes Fedora 32"
author = "Fcch"
date = "2020-05-25"
description = "Some notes for Fedora and version 32"
featured = true
tags = [
    "fedora",
    "bash"
]
categories = [
    "gnu-linux",
]
series = ["Fedora"]
thumbnail = "images/fedora-32/fedora32-400.jpg"
+++

When after update my Operating System [**Fedora 30**](https://getfedora.org/es/) to [**Fedora 32**](https://getfedora.org/es/), I was solving some problems with Docker CE, I custom the Prompt in my Console and change [**Gnome 3.36**](https://www.gnome.org/) to [**Cinnamon 4.4**](https://es.wikipedia.org/wiki/Cinnamon).

<!--more-->

![](/images/fedora-32/fedora32-400.jpg)

## Prompt Terminal

Some time ago I was looking for a way to have a prompt that shows me the time, the directory location and data about the state in a git repository (branches, changes), the objective is not use [**Oh-my-bash**](https://github.com/ohmybash/oh-my-bash), which is a set of scripts to customize the prompt where you can choose between various stiles options.

Searching, I found [**__git_ps1**](https://fedoraproject.org/wiki/Git_quick_reference), which is a script that allows you to obtain information about a git repository, it shows you the branch you are currently in, or if there is any change in said repository, I already had a problem solved and I only have to color the prompt get the time and the current directory path, I add these changes to my configuration file **~/.bashrc**, and it looks like this: 


When you have the git package installed, then you have the script in the operating system, if you don't have git installed then:

```cmd
# Install Git
$ sudo dnf install git

# Script /usr/share/git-core/contrib/completion/git-prompt.sh  
```

To color the prompt and obtain the desired data we have to add the following data:

**Data Description:**

```text
- __git_ps1: Script variable.
- t: Get the time, minutes and seconds.
- W: Shows name the current file.
- source: Script route __git_ps1.
- export GIT_PS1_SHOWCOLORHINTS: Data color for git.
- export GIT_PS1_SHOWDIRTYSTATE: Show the current state for repo.
- export GIT_PS1_SHOWUNTRACKEDFILES: Show untracked files.
- export PROMPT_COMMAND: Custom prompt.
- \[\033[0;31m\]: Red Color.
- \[\033[0;33m\]: Yellow Color.
- \[\033[0;32m\]: Green Color.
```

```bash
# Prompt personalizado ~/.bashrc
source /usr/share/git-core/contrib/completion/git-prompt.sh
export GIT_PS1_SHOWCOLORHINTS=true
export GIT_PS1_SHOWDIRTYSTATE=true
export GIT_PS1_SHOWUNTRACKEDFILES=true
export PROMPT_COMMAND='__git_ps1 "\[\033[01;33m\]\t\[\033[00m\] \[\033[01;31m\][\W]\[\033[00m\]" " \\\$ "'
```

## Docker CE

After installing Docker CE from the [**oficial**](https://docs.docker.com/engine/install/fedora/) repository and making the necessary configurations, it turns out that the containers had no internet access, after checking the internal Docker DNS, I found that I had an error with the firewall.

To solve the problem we add the interface **docker0** in the trusted zone of the firewall.

```cmd
$ sudo firewall-cmd --permanent --zone=trusted --add-interface=docker0
$ sudo firewall-cmd --reload
```

## Cinnamon 

Before upgrading to Fedora 32, the graphical environment I was using was Gnome 3, when presenting performance problems and high memory consumption I chose to change to Cinnamon 4.4, one of the important details is that the default driver for the video card is **NOUVEAU** open source driver, we installed **NVIDIA Corporation GF119 [NVS 310]** driver to improve video performance.

**NVIDIA Driver 390xxx - RPMFusion Repository**

```cmd
# Enable NVIDIA RPM Fusion
$ sudo dnf config-manager --set-enabled rpmfusion-nonfree-nvidia-driver

# List available drivers
$ sudo dnf repository-packages rpmfusion-nonfree-nvidia-driver info

# Install driver
$ sudo dnf install xorg-x11-drv-nvidia-390xx akmod-nvidia-390xx
$ sudo dnf install xorg-x11-drv-nvidia-390xx-cuda

# Test and control for performance
$ sudo dnf install -y gwe
```

## References

- [**Docker Fedora**](https://docs.docker.com/engine/install/fedora/)
- [**Fedora Git Reference**](https://fedoraproject.org/wiki/Git_quick_reference)