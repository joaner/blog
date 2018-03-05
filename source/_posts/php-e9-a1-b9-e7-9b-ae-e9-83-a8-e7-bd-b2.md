---
title: PHP项目部署
id: 96
categories:
  - linux
  - php
date: 2015-06-18 00:01:05
tags:
---

部署项目是一件比较繁琐的事情，不仅要配置目录权限，更新依赖库等，也要考虑到原子性操作，回滚等．而代码部署工具[deployer.org](http://deployer.org)提供了简化这些的方法，可以轻松编写PHP程序指导部署．

### 介绍

deploy是在客户端调用，它会自己登录到服务器执行相关操作．比如创建目录，重启fpm，只要给它**sudo (NOPASSWORD)**权限就可以．

它的思路是每次部署创建一个新目录，之前的几个版本目录还会保留下来，方便回滚，默认保存5个版本．

再通过软链接，用一个固定的目录比如 **current **指向线上版本，nginx只管读**current**，这样就不用改动网站配置．

### 安装

首先需要安装deployer，将它装在composer global下，方便部署多个项目
```
$ composer global require deployer/deployer:^3.0
```
程序就安装在 **~/.composer/vendor/bin/dep**

### 编写部署逻辑

我的项目使用的是Yii2框架，它的部署主要分三步：

1.  下载项目源代码
2.  更新 **composer **库
3.  调用 **./init** 脚本设置目录权限
deployer默认针对的是**symfony**项目的部署，与Yii2有差别．因此我需要禁用它的一些功能，比如目录权限配置，Yii2会自己完成这些配置．

在项目代码根目录下创建一个部署程序
```
vi deploy.php
```
```
<?php

// 包含必要的库
require 'recipe/symfony.php';

// 预定的安装目录
$path = '/srv/www/myproject';

// 要部署到的机器
server('prod', 'localhost', 22)
->user('name') // 登录主机的用户
->password(null) // 如果是null，会在安装前询问你密码
->stage('production')
// 需要先在这台机器上创建这个目录
->env('deploy_path', $path);

// 指定git仓库的位置，如果是私有的，可以根据HTTP协议设置登录用户
set('repository', 'https://[username]:[password]@github.com/my/project.git');

// 要执行的部署任务，deploy自带了一些逻辑比如更新代码和依赖库
task('deploy', [
    'deploy:prepare', // 登录服务器
    'deploy:release', // 创建发布目录
    'deploy:update_code', // 下载源代码
    'deploy:vendors', // 更新composer
]);

// 我们也可以自定义部署的逻辑，比如调用Yii的init脚本初始化目录权限
task('init', function() {
    // {{release_path}} 是deployer定义的变量，指程序部署到的目录位置
    run("{{release_path}}/init --env='Production' --overwrite=All");
});
// 更新线上目录current的源目录指向
task('push', function() {
    // {{deploy_path}} 是指整个项目的位置，等于 $path
    run("ln -sfn {{release_path}} {{deploy_path}}/current");
});

// 更新opcache对文件的缓存，因为nginx传入的document_root是虚拟链接，所以不会改变，需要手动清空缓存
task('cache', function() {
    run('/usr/bin/env php -r "opcache_reset();"');
});

// 在自带的部署任务完成后，执行自定义的逻辑
// 更新yii2目录权限为线上配置
after('deploy', 'init');
// 更新指向线上目录的位置，这里才正式发布了
after('deploy', 'push');
// 更新opcache缓存，否则程序更改不会生效
after('deploy', 'cache');

```

#### 目录说明

**/srv/www/myproject**

设定的项目部署位置

**{{release_path}}**

它是为了方便而自动创建的一个软链接目录，总是指向正在部署中的那个目录（**releases** 下）

**{{deploy_path}}/current**

线上目录，它也是一个软链接．不过在部署完成后，它才会更新．因此它是原子性的，不会影响线上访问．

### 开始部署

参数 **deploy** 就是刚才创建的部署程序文件名，**production**就是按线上生产环境部署
```
$ ~/.composer/vendor/bin/dep deploy production
✔ Executing task deploy:prepare
✔ Executing task deploy:release
✔ Executing task deploy:update_code
✔ Executing task deploy:clear_controllers
✔ Executing task deploy:vendors
✔ Executing task init
✔ Executing task push
✔ Executing task cache

```
部署后可以看到这样的目录结构
```
$ tree -L 2 /srv/www/myproject
.
|-- current -> /srv/www/myproject/releases/20150617163027
|-- release -> /srv/www/myproject/releases/20150617163027
|-- releases
|   |-- 20150617160840
|   |-- 20150617161024
|   |-- 20150617161702
|   |-- 20150617162823
|   `-- 20150617163027


```

#### nginx的配置

```
server {
      root /srv/www/myproject/current;
      ......
}
```
&nbsp;

至此可以实现不影响线上访问，原子性的部署．更多文档参见  [http://deployer.org/docs](http://deployer.org/docs)