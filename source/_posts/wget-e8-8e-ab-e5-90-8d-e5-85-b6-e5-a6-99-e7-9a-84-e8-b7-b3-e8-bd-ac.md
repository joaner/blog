---
title: wget莫名其妙的跳转
id: 127
categories:
  - linux
date: 2015-07-15 15:34:06
tags:
---

尝试从自己的服务器下载一些包文件，服务端也正常响应200，但wget却奇怪的收到302跳转请求。

客户端下载的命令
```
$ wget http://package.xiaoai.me/php-5.6.10.tar.gz --no-cache --no-dns-cache
--2015-07-15 15:24:37--  http://package.xiaoai.me/php-5.6.10.tar.gz
正在解析主机 package.xiaoai.me (package.xiaoai.me)... 104.155.200.103
正在连接 package.xiaoai.me (package.xiaoai.me)|104.155.200.103|:80... 已连接。
已发出 HTTP 请求，正在等待回应... 302 Found
位置：http://124.205.69.137/files/40050000012FFD2C/cn2.php.net/distributions/php-5.6.10.tar.gz [跟随至新的 URL]
--2015-07-15 15:24:37--  http://124.205.69.137/files/40050000012FFD2C/cn2.php.net/distributions/php-5.6.10.tar.gz
正在连接 124.205.69.137:80... 已连接。
已发出 HTTP 请求，正在等待回应... 200 OK
长度：18306403 (17M) [application/octet-stream]
正在保存至: “php-5.6.10.tar.gz.2”

php-5.6.10.tar.gz.2  82%[=================>    ]  14.45M  3.59MB/s   eta 1s    

```
刚开始没注意，还以为国外的服务器速度这么快，原来是跳转了。

而服务端的nginx的配置，就是一个普通网站
```
server {
    listen 80;
    server_name package.xiaoai.me;
    root /srv/package;

    access_log logs/package.xiaoai.me.access.log;

    location / {
        index index.html;
    }
}

```
nginx的日志显示的是200，不是302:
```
xx.xx.xx.xx - - [15/Jul/2015:15:24:37 +0800] "GET /php-5.6.10.tar.gz HTTP/1.1" 200 15488 "-" "Wget/1.16.1 (linux-gnu)"

```
&nbsp;

那这个302从哪里来的，未解之迷。暂时记录下，有时间看看