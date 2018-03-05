---
title: 自定义git-shell命令
id: 141
categories:
  - linux
date: 2015-07-22 23:57:59
tags:
---

之前写过搭建自己的git服务器，其中提到了使用git-shell来限制git用户的权限．参见: **[搭建自己的git服务器](https://xiaoai.me/?p=70 "编辑“搭建自己的git服务器”") **https://xiaoai.me/?p=70

默认的，git-shell只提供了**list**命令，即显示仓库列表．有时还需要扩展它的功能，比如创建一个新的空仓库：

## 创建命令

只需要在git-shell目录下增加一个可执行文件，比如就叫 **create**
```
$ cd /home/git
$ sudo vi git-shell-commands/create
#/bin/sh
git init --bare $1.git;
$ sudo chmod a+x git-shell-commands/create

```
这里的**/home/git**就是git用户的主目录，也就是存放所有仓库的目录．**git-shell-commands/create **就是创建新仓库的脚本，它的参数 **$1 **就是传入的新仓库名

## 调用命令

```
$ ssh git@xiaoai.me
Run 'help' for help, or 'exit' to leave.  Available commands:
create
list
git> create hello
Initialized empty Git repository in /home/git/hello.git/

```
至此就算扩展了一个新功能，挺简单的