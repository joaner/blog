---
title: centos安装旧版本gcc
id: 24
categories:
  - linux
date: 2014-07-10 11:55:19
tags:
---

编译方式非常麻烦，因此我选择rpm安装。

1\. 卸载当前的gcc高版本，centos6.5的gcc默认是 4.4.7 ，我要安装 4.1.2
<pre class="lang:sh decode:true ">sudo yum remove cpp</pre>
&nbsp;

2\. 下载rpm包，一个是 gcc，一个是cpp
<pre class="lang:sh decode:true ">wget ftp://mirror.nl.leaseweb.net/plesk/PSA_9.5.4/update-rpm-CentOS-5-x86_64/cpp-4.1.2-48.el5.x86_64.rpm

wget ftp://ftp.pbone.net/mirror/ftp.scientificlinux.org/linux/scientific/55/x86_64/SL/gcc-4.1.2-48.el5.x86_64.rpm</pre>
&nbsp;

3\. 安装，先安装 cpp
<pre class="lang:sh decode:true ">rpm -ivh cpp-4.1.2-48.el5.x86_64.rpm

rpm -ivh gcc-4.1.2-48.el5.x86_64.rpm</pre>
&nbsp;