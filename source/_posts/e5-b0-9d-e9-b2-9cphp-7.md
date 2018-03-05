---
title: 尝鲜PHP 7
id: 45
categories:
  - php
date: 2015-05-15 06:27:06
tags:
---

php7目前还处于开发阶段，有很多扩展模块不支持．当然有一些新特性还是很值得玩玩的．

文档可以在 [https://wiki.php.net/phpng](https://wiki.php.net/phpng) 看到

## 安装

普通用户需要从https协议下载，ssh协议只面向PHP小组开发者
```
git clone https://git.php.net/repository/php-src.git
```
```
./buildconf
./configure --prefix=/srv/server/php7 --enable-mbstring --enable-zip --enable-bcmath --enable-pcntl --enable-ftp --enable-exif --enable-calendar --enable-sysvmsg --enable-sysvsem --enable-sysvshm --enable-wddx --with-curl --with-mcrypt --with-iconv --with-gmp --with-pspell --with-gd --with-jpeg-dir=/usr --with-png-dir=/usr --with-zlib-dir=/usr --with-xpm-dir=/usr --with-freetype-dir=/usr --with-t1lib=/usr --enable-gd-native-ttf --enable-gd-jis-conv --with-openssl --with-mysql=/usr --with-pdo-mysql=/usr --with-gettext=/usr --with-zlib=/usr --with-bz2=/usr --with-recode=/usr --with-mysqli=/usr/bin/mysql_config

```

#### 在安装时可能遇到的依赖问题

```
checking for re2c... no
configure: WARNING: You will need re2c 0.13.4 or later if you want to regenerate PHP parsers.

```

##### 需要安装[re2c库](http://re2c.org/)

```
wget http://downloads.sourceforge.net/project/re2c/re2c/0.14.2/re2c-0.14.2.tar.gz?r=&amp;ts=1431640121&amp;use_mirror=tcpdiag

```
接下来configure 和 make 不多写了

###### 其它问题 bison, bzip2, t1lib, pspell, RECODE

```
configure: error: bison is required to build PHP/Zend when building a GIT checkout!
# yum install bison bison-devel

configure: error: Please reinstall the BZip2 distribution
# yum install bzip2 bzip2-devel

configure: error: Your t1lib distribution is not installed correctly. Please reinstall it.
# yum install t1lib t1lib-devel

configure: error: Unable to locate gmp.h
# yum install gmp-devel

configure: error: Cannot find pspell
# yum install aspell aspell-devel

configure: error: Can not find recode.h anywhere under /usr /usr/local /usr /opt.
# yum install recode recode-devel
```
不同的环境可能还有其它依赖问题，不过都可以google解决的

好了，configure 完成
```
Generating files
configure: creating ./config.status
creating main/internal_functions.c
creating main/internal_functions_cli.c
+--------------------------------------------------------------------+
| License:                                                           |
| This software is subject to the PHP License, available in this     |
| distribution in the file LICENSE.  By continuing this installation |
| process, you are bound by the terms of this license agreement.     |
| If you do not agree with the terms of this license, you must abort |
| the installation process at this point.                            |
+--------------------------------------------------------------------+
Thank you for using PHP.
config.status: creating php7.spec
config.status: creating main/build-defs.h
config.status: creating scripts/phpize
config.status: creating scripts/man1/phpize.1
config.status: creating scripts/php-config
config.status: creating scripts/man1/php-config.1
config.status: creating sapi/cli/php.1
config.status: creating sapi/cgi/php-cgi.1
config.status: creating ext/phar/phar.1
config.status: creating ext/phar/phar.phar.1
config.status: creating main/php_config.h
config.status: executing default commands
configure: WARNING: unrecognized options: --with-mysql

```
接下来就是漫长的make
```
$ make
# make install
```

##### 大功告成!

```
$ /srv/server/php7/bin/php -v
PHP 7.0.0-dev (cli) (built: May 14 2015 18:30:21) 
Copyright (c) 1997-2015 The PHP Group
Zend Engine v3.0.0-dev, Copyright (c) 1998-2015 Zend Technologies
```

## 运行

### 路径说明

php源码包放置在 `/srv/package/php-7`
php安装在 `/srv/server/php7`

### 设置配置文件

```
cp /srv/package/php-7/php.ini-development /srv/server/php7/lib/php.ini
```

### 作为CGI模式运行在后台

```
nohup /srv/server/php7/bin/php-cgi -b 127.0.0.1:9002 -c /srv/server/php7/lib/php.ini > /var/log/php-cgi.log &amp;
```
wordpress 已经支持运行在php7上了

### 优化点

在编译时可以启用fpm --enable-fpm 这样管理php-cgi进程就方便多了