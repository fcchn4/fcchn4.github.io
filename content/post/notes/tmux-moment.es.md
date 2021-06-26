+++
title = "Momento Tmux"
author = "Fcch"
date = "2021-05-25"
description = "Guía simple de comandos Tmux"
featured = true
tags = [
    "bash",
]
categories = [
    "apuntes",
]
series = ["Linux"]
thumbnail = "images/tmux-moment/tmux-00.png"
+++

**tmux** es un multiplexor de terminales, permite crear, acceder y controlar varios terminales desde una única pantalla. **tmux** puede desconectarse de una pantalla y continuar ejecutándose en segundo plano, y luego se vuelve a colocar.

<!--more-->

![](/images/tmux-moment/tmux-00.png)

## Lo Básico

Primero debemos ejecutar el comando **tmux**, dentro del mismo podemos realizar diferentes acciones, para estas acciones debemos enviarle comandos a **tmux**, en mi caso utilizare la combinación **ctrl + b** que es la configuración por defecto luego de la instalación:

| **Descripción**                | **Comando**               |
| :----------------------------- | :------------------------ |
| Dividir terminal en horizontal | ctrl + b + "              |
| Dividir terminal en vertical   | ctrl + b + %              |
| Cambiar a panel izquierda      | ctrl + b + keys izquierda |
| Cambiar a panel derecha        | ctrl + b + keys derecha   |
| Cambiar a panel arriba         | ctrl + b + keys arriba    |
| Cambiar a panel abajo          | ctrl + b + keys abajo     |
| Ver numero de terminal         | ctrl + b + q              |
| Saltar de un panel a otro      | ctrl + b + o              |
| Cerra panel actual             | ctrl + b + x              |
| Cerra la ventana actual        | ctrl + b + &              |
| Recorre los diseños de paneles | ctrl + b + space          |
| Cerra panel actual             | ctrl + b + x              |
| Ayuda tmux                     | ctrl + b + ?              |
| Listar todas las sesiones      | tmux ls                   |
| Versión                        | tmux -V                   |

## Funcionalidades muy útiles

Un función muy útil en **tmux** es el **modo comados**, que nos permite ingresar comandos que nos faciliten tareas de forma mas simple.

| **Descripción**                        | **Comando**                 |
| :------------------------------------- | :-------------------------- |
| Modo comando                           | ctrl + b + :                |
| Activa sincronización de paneles       | :setw synchronize-panes on  |
| Desactiva sincronización de paneles    | :setw synchronize-panes off |
| Redimencionar panel hacia arriba       | :resize-pane -U             |
| Redimencionar panel hacia abajo        | :resize-pane -D             |
| Redimencionar panel hacia la izquierda | :resize-pane -L             |
| Redimencionar panel hacia la derecha   | :resize-pane -R             |
| Redimencionar panel hacia arriba       | :resize-pane -U 10          |
| Redimencionar panel hacia abajo        | :resize-pane -D 10          |
| Redimencionar panel hacia la izquierda | :resize-pane -L 10          |
| Redimencionar panel hacia la derecha   | :resize-pane -R 10          |

## Algunas demos

Ejecutamos **tmux**, dividimos en diferentes paneles:

![](/images/tmux-moment/tmux-01.gif)

Sincronización de paneles:

![](/images/tmux-moment/tmux-02.gif)

Trabajo con paneles y redimenciones con **ctrl + b + space** y **ctrl + b + q**:

![](/images/tmux-moment/tmux-03.gif)

## Referencias

- [**Tmux Wiki**](https://github.com/tmux/tmux/wiki)
- [**Tmux**](https://github.com/tmux/tmux)