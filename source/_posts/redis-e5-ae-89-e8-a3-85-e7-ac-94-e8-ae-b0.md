---
title: redis安装笔记
id: 75
categories:
  - linux
date: 2015-06-10 01:22:49
tags:
---

## 安装

安装前约定以 **/srv/server/redis** 来放置编译后的可执行文件

下载解压最新版开发包，因为本身是开发用的，所以装个**unstable**玩玩
<pre>$ wget https://github.com/antirez/redis/archive/unstable.tar.gz
$ tar -zxf unstable.tar.gz
$ cp -R redis-unstable /srv/server/redis
</pre>
redis本身小巧，不需要特别的依赖关系，所以省去了**./configure**这一步，在哪**make**就安装在哪
<pre>$ cd /srv/server/redis
$ sudo make
$ sudo make install</pre>
一般都不会遇到错误，因为它没有过多的依赖问题

安装完成，在**./src**目录下就有编译好的可执行文件．另外安装程序还会在**/usr/local/bin**也放置一份可执行文件，因而可以直接运行 **$ redis-cli**

## 优化点

将redis-server作为init.d/service管理会方便很多，而redis也自带了启动脚本

### 首先复制文件到预定位置

<pre>$ sudo cp ./utils/redis_init_script /etc/init.d/redis
$ sudo mkdir /etc/redis
$ sudo cp ./redis.conf /etc/redis/6379.conf</pre>

### 在后台运行

redis默认在当前进程直接运行，因此每回启动都需要用nohup，比较繁琐．可以改下它的配置文件解决
<pre>$ sudo vi /etc/redis/6379.conf
- daemonize no
+ daemonize yes</pre>

### 启动控制

redis的启动脚本和配置文件定义的pid位置还有差别
<pre>$ sudo vi /etc/redis/6379.conf
- pidfile /var/run/redis.pid
+ pidfile /var/run/redis_6379.pid
$ sudo service redis start
Starting Redis server...
$ sudo service redis stop
Stopping ...
Redis stopped</pre>

### 开机启动

redis的启动文件不支持**chkconfig**配置，需要在开头加两行启动配置

    <span class="com">$ sudo vi /etc/init.d/redis
    &lt;&lt;&lt;++
    # chkconfig: 2345 90 60
    # description start redis server on boot
    &gt;&gt;&gt;
    $ sudo chkconfig redis on</span>

大功告成

## 后续

redis从各方面都比memcached强，尤其是在面对复杂业务时，完爆memcached

后面会再写些笔记，记录redis的使用经验