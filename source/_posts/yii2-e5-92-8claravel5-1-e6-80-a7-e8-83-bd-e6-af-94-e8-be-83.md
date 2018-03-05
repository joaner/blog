---
title: yii2和laravel5.1性能比较
id: 159
categories:
  - php
date: 2015-08-02 17:29:07
tags:
---

周末对yii2和laravel5.1进行了简单的性能测试，结果是yii2比laravel5.1快很多。

## 测试环境

### 系统

*   Fedora 22
*   linux: Linux 4.0.8-300.fc22.x86_64
*   gcc: 版本 5.1.1 20150618 (Red Hat 5.1.1-4) (GCC)
*   Mem: 8 GiB
*   cpu: Intel® Core™ i7-4702MQ CPU @ 2.20GHz × 8

### 框架

*   laravel 5.1
*   yii 2.0 (basic类型，它和advanced区别主要在于目录结构，就是少include了两三个配置文件，性能差距不大)

### 环境

*   统一都是生产环境，关闭了debug。
*   语言包等其他配置都选用默认的配置

### 测试代码

定义一个新的控制器，简单的输出 hello 字符

## 测试

我每次用**ab -n 3000**来测试，分成多次交叉测试，得出的结果都差不多。

### 结果

在默认情况下，简单的输出hello程序下，yii2的性能是laravel5.1的4倍多！

我原先以为差不了多少，但真的很意外。不过laravel我不太熟悉，也许有哪些可以优化的地方，但在默认配置下，yii2确实完爆laravel。

当然评价框架不能只看性能，我用yii2做过三四个项目，它在功能和设计方面也很强大，扩展性十足。是我最爱的框架！

## 不公平的地方

yii2默认不会设置session和XSRF-TOKEN，但laravel会，所以主要性能差距应该就是这个造成的。但从设计上，yii2总是按需加载，只有当使用视图的**ActiveForm**时它才会主动设置**XSRF**，显然比laravel要灵活。

所以我也给yii加上了启用session的代码：`\Yii::$app->session->open();`，但仍然比laravel快4倍多。结果见最下。

## 原因

至于为什么差距这么大，下次有时间再看吧。已知的差别：

*   laravel自己实现了session，比起yii2用的原生的要慢，但可能安全性更高。
&nbsp;

### 附测试数据

ab测试3000次请求，这里各列出随机的一组测试

#### laravel 5.1

```
$ ab -n 3000 http://laravel.localhost/test
Concurrency Level:      1
Time taken for tests:   19.507 seconds
Complete requests:      3000
Failed requests:        0
Total transferred:      2952398 bytes
HTML transferred:       15000 bytes
Requests per second:    153.79 [#/sec] (mean)
Time per request:       6.502 [ms] (mean)
Time per request:       6.502 [ms] (mean, across all concurrent requests)
Transfer rate:          147.81 [Kbytes/sec] received

```

#### Yii 2.0 （默认配置，不开启session）

```
$ ab -n 3000 http://yii2.localhost/test
Concurrency Level:      1
Time taken for tests:   4.456 seconds
Complete requests:      3000
Failed requests:        0
Total transferred:      501000 bytes
HTML transferred:       15000 bytes
Requests per second:    673.24 [#/sec] (mean)
Time per request:       1.485 [ms] (mean)
Time per request:       1.485 [ms] (mean, across all concurrent requests)
Transfer rate:          109.80 [Kbytes/sec] received

```

#### Yii 2.0 （为了和laravel保持一致，所以也开启session）

```
Concurrency Level:      1
Time taken for tests:   5.045 seconds
Complete requests:      3000
Failed requests:        0
Total transferred:      1116000 bytes
HTML transferred:       15000 bytes
Requests per second:    594.67 [#/sec] (mean)
Time per request:       1.682 [ms] (mean)
Time per request:       1.682 [ms] (mean, across all concurrent requests)
Transfer rate:          216.03 [Kbytes/sec] received
```
&nbsp;