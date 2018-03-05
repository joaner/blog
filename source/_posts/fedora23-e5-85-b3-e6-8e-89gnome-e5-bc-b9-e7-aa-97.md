---
title: fedora23关掉gnome弹窗
id: 289
categories:
  - linux
date: 2016-02-05 13:07:35
tags:
---

我用的桌面是最新的fedora23，它有时候会莫名其妙的弹出gnome的网页弹窗。

经过对进程排查，弹窗的进程文件是 `/usr/libexec/gnome-shell-portal-helper`，所以只要删除这个执行文件就可以屏蔽了。