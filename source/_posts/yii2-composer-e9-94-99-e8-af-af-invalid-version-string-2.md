---
title: 'yii2 composer错误: Invalid version string &quot;2.*&quot;'
id: 155
categories:
  - php
date: 2015-08-02 15:59:57
tags:
---

在更新或安装yii2时，现在会出现一个composer库的错误：
<pre>$ composer global require "fxp/composer-asset-plugin:~1.0.0"
$ composer create-project --prefer-dist yiisoft/yii2-app-basic basic
Installing yiisoft/yii2-app-basic (2.0.4)
  - Installing yiisoft/yii2-app-basic (2.0.4)
    Downloading: 100%         

Created project in basic
Loading composer repositories with package information
Installing dependencies (including require-dev)

  [UnexpectedValueException]                                              
  Could not parse version constraint &lt;=2.*: Invalid version string "2.*"  

</pre>
这是yii2依赖库 **fxp/composer-asset-plugin** 的问题，要解决这个问题，需要升级它。

yii2官网上让我们使用的是 1.0.0版本，现在要使用最新的dev-master版本才行。可以直接运行：
<pre>$ composer.phar global require "fxp/composer-asset-plugin:1.0.*@dev"</pre>
它会覆盖掉原先global全局有问题的1.0.0版本配置

然后重新 **composer update** 即可

参考：

[http://stackoverflow.com/questions/31681630/yii2-composer-update-error-invalid-version-string-2](http://stackoverflow.com/questions/31681630/yii2-composer-update-error-invalid-version-string-2)