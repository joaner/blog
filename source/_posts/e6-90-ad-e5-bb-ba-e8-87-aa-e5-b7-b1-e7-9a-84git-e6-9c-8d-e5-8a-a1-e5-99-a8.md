---
title: 搭建自己的git服务器
id: 70
categories:
  - linux
date: 2015-06-07 01:46:57
tags:
---

github私有仓库的费用太贵，有VPS可以自己搭建一个专属git服务器

其实没什么需要安装的，git的默认协议SSH不需要什么配置，主要是方便和安全方面的优化

## 初始化环境

为了安全，我们为git仓库专门设置一个管理用户**git**，约定使用 **/srv/git **放置仓库
<pre>$ sudo useradd \
--create-home \
--home-dir=/srv/git \
--root=/srv/git \
--base-dir=/srv/git \
--shell=/usr/bin/git-shell \
git
$ sudo passwd git</pre>

### 上面的参数作用

<pre>--create-home --home-dir=/srv/git</pre>
创建用户主目录，将来可以添加SSH KEY登录方式，和代码仓库放在一起方便管理
<pre>--root=/srv/git</pre>
限制这个用户的访问目录在/srv/git，不能访问其它路径，这是出于安全考虑
<pre>--base-dir=/srv/git</pre>
用户base目录，即默认的父目录．帮助简化了克隆时的目录前缀
<pre>--shell=/usr/bin/git-shell</pre>
只允许用户用于管理仓库，不得执行其它命令

## 创建仓库

初始化一个裸仓库，即只包含仓库信息，不包含代码
<pre>$ sudo git --bare init /srv/git/MY_PROJECT.git</pre>
不需要其它配置了，就这么简单

## 从客户端克隆

MYHOST就是VPS的主机地址
<pre>$ git clone git@MYHOST:MY_PROJECT.git</pre>

#### 主机名后的目录只需要填写仓库目录

这是创建用户时指定的 **--base-dir** 立的功，让你不用输入完整的目录 **/srv/git/MY_PROJECT.git**

#### 尝试用git用户登录会被阻止

<pre>$ ssh git@MYHOST
git@MYHOST's password: 
fatal: Interactive git shell is not enabled.hint: ~/git-shell-commands should exist and have read and execute access.
Connection to MYHOST closed.</pre>

## web界面

#### 首先安装gitweb软件包，它附带了访问仓库的cgi动态程序．

gitweb默认需要在 **/var/www/git** 下放置cgi程序和仓库代码，但我们把仓库安装在了 **/srv/git** 下，所以做个软链接
<pre># ln -s /srv/git /var/www/git
# yum install gitweb
# ls /var/www/git
gitweb.cgi static PROJECT.git
</pre>
可以看到yum安装后多出了这两个: **gitweb.cgi static**

#### 接下来启动gitweb服务

刚才安装的gitweb只是一套动态程序，HTTP访问还要借助lighttpd apache等web服务器．而git自带的命令instaweb可以自动创建web服务器的配置文件．指定--httpd参数为lighttpd即可
<pre># sudo -i
# cd /var/www/git
# git init

# yum install lighttpd
# git instaweb --httpd=lighttpd --port=1234
No known browser available.
http://127.0.0.1:1234
</pre>

##### 上面的命令解释:

<pre># sudo -i
# cd /var/www/git</pre>
因为 /var/www/git 是git用户的home目录，因此普通用户可能没有权限cd，所以需要-i参数保持root权限.

注意：git instaweb 不能指定document root目录，所以在哪个目录下执行，就展示那个目录下的仓库．因而需要先**cd**到这里
<pre># git init</pre>
git instaweb创建的web服务器配置文件，需要一个git仓库来存放．

#### 外界访问控制

到这里就可以访问git仓库了，但没有完，还需要外界可以访问，并且要限制访问．这时可以借助nginx，或者修改lighttpd的配置文件
<pre>server {
    listen 80;
    server_name git.MYHOST.COM
    location / {
        auth_basic "git web require auth";
        auth_basic_user_file /srv/etc/passwd;
        proxy_pass http://127.0.0.1:1234/;
    }
}
</pre>
有关auth_basic的配置可以参阅 [http://nginx.org/en/docs/http/ngx_http_auth_basic_module.html](http://nginx.org/en/docs/http/ngx_http_auth_basic_module.html)

#### 大功告成

到这一步就结束了，现在访问 http://git.MYHOST.COM/ 即可浏览仓库信息

如果你觉得这个web界面太丑陋的话，可以看看 [https://github.com/kogakure/gitweb-theme](https://github.com/kogakure/gitweb-theme) ，这个项目提供了更优雅的主题界面：[http://kogakure.github.io/gitweb-theme/](http://kogakure.github.io/gitweb-theme/)