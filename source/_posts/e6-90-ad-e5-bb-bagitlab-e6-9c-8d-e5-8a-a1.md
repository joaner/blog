---
title: 搭建gitlab服务
id: 314
categories:
  - linux
date: 2016-06-06 07:24:51
tags:
---

为了搭建这个折腾了一天

## 有两种安装方式

### YUM包快速安装

官方推荐，但是集成了nginx,redis,pgsql，需要占用300M的空间。

### gitlab源码配置安装

需要自己安装配置redis,pgsql/mysql。这样轻便很多，但是流程非常复杂

* * *

我选择了YUM快速安装的方式，按照[centos7安装文档](https://about.gitlab.com/downloads/#centos7)来的。但是有个别地方出差错，这里整理一下。

## 问题整理

我遇到的大多数问题，都是sock文件的权限不够，比如`redis.sock`需要加上`chmod a+rx`权限才行。

### 访问502错误

这个需要再等一分钟确认，如果还是502，则说明主服务启动出错了，也可能是ruby服务的sock文件权限问题

```
/var/log/gitlab/nginx/gitlab_error.log
```

### 访问500错误

可能是`redis`获取`pgsql`启动出错，查看 `production.log` 日志即可

```
/var/log/gitlab/gitlab-rails/production.log
```

我遇到的是redis.sock的权限有误

```
sudo chmod a+rw /var/opt/gitlab/redis/redis.socket
```

### gitlab访问主机名的配置

编辑 `/opt/gitlab/embedded/service/gitlab-rails/config/gitlab.yml`的`gitlab.host`项目

记得重启服务，需要等待1分钟：

```
sudo gitlab-ctl restart
```

接下来就可以用 http://git.xiaoai.me 访问了。

如果想支持HTTPS，需要改下gitlab-nginx的配置`/var/opt/gitlab/nginx/conf/gitlab-http.conf`。或者给`gitlab-nginx`外层再套一个nginx，也好兼容机器上的其他网站。
