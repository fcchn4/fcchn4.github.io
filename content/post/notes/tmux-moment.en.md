+++
title = "Tmux Moment"
author = "Fcch"
date = "2021-05-25"
description = "Simple guide for Tmux commands"
featured = true
tags = [
    "linux",
    "bash"
]
categories = [
    "gnu-linux",
]
series = ["Tmux"]
thumbnail = "images/tmux-moment/tmux-00.png"
+++

**tmux** is a terminal multiplexer, it enables a number of terminals to be created, accessed, and controlled from a single screen. **tmux** may be detached from a screen and continue running in the background, then later reattached.

<!--more-->

![](/images/tmux-moment/tmux-00.png)

## The Basics

First we must execute the command **tmux**, within it we can perform different actions, for these actions we must send commands to **tmux**, in my case I will use the combination **ctrl + b** which is the configuration by default after installation:

| **Description**                | **Command**           |
| :----------------------------- | :-------------------- |
| Split terminal horizontally    | ctrl + b + "          |
| Split terminal vertically      | ctrl + b + %          |
| Switch to left panel           | ctrl + b + keys left  |
| Switch to right panel          | ctrl + b + keys right |
| Switch to panel up             | ctrl + b + keys up    |
| Switch to panel down           | ctrl + b + keys down  |
| See terminal number            | ctrl + b + q          |
| Jump from one panel to another | ctrl + b + o          |
| Close current panel            | ctrl + b + x          |
| Close the current window       | ctrl + b + &          |
| Cycle through panel layouts    | ctrl + b + space      |
| Close current panel            | ctrl + b + x          |
| Help tmux                      | ctrl + b + ?          |
| List all sessions              | tmux ls               |
| Version                        | tmux -V               |

## Very Useful Features

A very useful function in **tmux** is the **command mode**, which allows us to enter commands that facilitate tasks in a simpler way.

| **Description**                        | **Command**                 |
| :------------------------------------- | :-------------------------- |
| Command mode                           | ctrl + b + :                |
| Activate panel synchronization         | :setw synchronize-panes on  |
| Turn off panel sync                    | :setw synchronize-panes off |
| Resize panel up                        | :resize-pane -U             |
| Resize panel down                      | :resize-pane -D             |
| Resize panel to the left               | :resize-pane -L             |
| Resize panel to the right              | :resize-pane -R             |
| Resize panel up 10                     | :resize-pane -U 10          |
| Resize panel down 10                   | :resize-pane -D 10          |
| Resize panel to the left 10            | :resize-pane -L 10          |
| Resize panel to the right 10           | :resize-pane -R 10          |

## Some Demos

We execute **tmux**, we divide into different panels the terminal:

![](/images/tmux-moment/tmux-01.gif)

Panel synchronization:

![](/images/tmux-moment/tmux-02.gif)

Work with panels and resizes with **ctrl + b + space** and **ctrl + b + q**:

![](/images/tmux-moment/tmux-03.gif)

## References

- [**Tmux Wiki**](https://github.com/tmux/tmux/wiki)
- [**Tmux**](https://github.com/tmux/tmux)