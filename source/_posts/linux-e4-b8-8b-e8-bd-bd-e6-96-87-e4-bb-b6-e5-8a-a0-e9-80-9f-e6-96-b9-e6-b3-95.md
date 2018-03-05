---
title: linux下载文件加速方法
id: 278
categories:
  - linux
date: 2016-01-24 19:31:01
tags:
---

有时候要下载一些墙外的文件会非常慢，比如github的仓库包，因为存放在AWS上，国内下载速度很慢。

下面是我整理了一些加速的方法：

## nginx搭建正向代理服务器

首先需要一个海外的服务器，这里推荐Google Computer Engine (台湾)的机器，大约$7/月最低配的机器即可。

这里搭建了一个nginx的正向代理，通过链接中的规则请求下载地址。

配置规则：

```
    server {
        listen    80;
        server_name ~^(?<domain>.+)\.proxy.xiaoai.me$;

        location / {
            resolver 8.8.8.8;
            proxy_pass $scheme://$domain;
            proxy_set_header Host $domain;
            proxy_pass_request_headers      on;
        }
    }
```

比如想下载：https://nodejs.org/dist/v5.5.0/node-v5.5.0-linux-x64.tar.xz  很慢
这时候可以给链接加个正向代理的域名： http://nodejs.org.proxy.xiaoai.me/dist/v5.5.0/node-v5.5.0-linux-x64.tar.xz 通过代理，速度会更快。

## proxychains 命令行设置代理

有些情况下无法更改域名，比如composer更新。这时候可以通过proxychains配置SOCKS5或HTTP代理服务，然后在命令行加速。

```
$ proxychains composer update
```

proxychains的方式不是全局的，所以只针对后面命令的进程设置代理配置。不会影响其它的进程，非常有用。

## Axel 多线程下载

axel是一个命令行下的工具。比起`wget`，它支持同时开启多个HTTP连接。利用HTTP的分段传输特性`Range: bytes=1024-2048`实现了加速下载。
