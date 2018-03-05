---
title: 搭建自己的web邮件管理
id: 61
categories:
  - linux
date: 2015-06-06 15:09:47
tags:
---

当搭建了自己的邮件服务器后，还需要发送和管理邮件的服务．客户端foxmail提供了这样的服务，而浏览器端可以使用Rainloop-webmail开源软件．

### 项目地址

##### 官网

[http://www.rainloop.net/](http://www.rainloop.net/)

##### github

[https://github.com/RainLoop/rainloop-webmail](https://github.com/RainLoop/rainloop-webmail)

##### 界面截屏

http://www.rainloop.net/screenshots/

### 特点介绍

*   支持多语言，包括中文的完整支持
*   基于PHP5.3+，满足大部分主机
*   不需要数据库（使用联系人功能才需要数据库）
*   内置多种主题，界面美观

### 系统需求

> **Web server: **[Apache](http://httpd.apache.org/), [nginx](http://nginx.org/), [lighttpd](http://www.lighttpd.net/), [MS IIS](http://www.iis.net/) or other with PHP support> 
> 
> **PHP:** 5.3 and above> 
> 
> **PHP extensions: **[cURL](http://php.net/manual/en/book.curl.php), [iconv](http://php.net/manual/en/book.iconv.php), [json](http://php.net/manual/en/book.json.php), [libxml](http://php.net/manual/en/book.libxml.php), [dom](http://php.net/manual/en/book.dom.php), [openssl](http://php.net/manual/en/book.openssl.php), [DateTime](http://php.net/manual/en/book.datetime.php), [PCRE](http://php.net/manual/en/book.pcre.php), [SPL](http://php.net/manual/en/book.spl.php)> 
> 
> **Browser: **[Internet Explorer 9+](http://windows.microsoft.com/en-US/internet-explorer/download-ie), [Firefox](http://www.mozilla-europe.org/), [Opera 10+](http://www.opera.com/), [Safari 3+](http://www.apple.com/safari/), [Google Chrome](http://www.google.com/chrome/)> 
> 
> **Optional: **[PDO](http://php.net/manual/en/book.pdo.php) ([MySQL](http://php.net/manual/en/ref.pdo-mysql.php)/[PostgreSQL](http://php.net/manual/en/ref.pdo-pgsql.php)/[SQLite](http://php.net/manual/en/ref.pdo-sqlite.php)) PHP extension (for contacts)
PHP的扩展，除了openssl，curl安装时需要特别指定 `--with-openssl``--with-curl` ,其余扩展都是默认安装的．

&nbsp;

## 搭建

##### 安装文档

http://www.rainloop.net/docs/installation/

##### 特别注意

###### 创建数据目录

需要在安装目录外创建一个data目录，web服务可读写权限．这个目录包含了许多关键信息，需要配置禁止访问
<pre>location ^~ /data {
  deny all;
}</pre>

###### 安装后配置

首先需要访问 http://mail.example.com/?admin 配置允许的域名

初始登录 admin 密码 12345 ,登录后先改掉admin密码

然后进入 domains 页面: /?/admin#/domains  将你的邮箱域名添加到列表里.然后才能用这个邮箱地址登录

###### 更换语言

进入general页面: /?/admin#/general 前台界面支持中文,后台界面不支持

###### 登录

用完整的邮箱密码登录即可，如果不想每次输入邮箱的域名，可以在后台域名管理设置为其默认域名．点击域名列表里的域名,勾选 Use short login

这样就可以只输入邮箱@前的用户名