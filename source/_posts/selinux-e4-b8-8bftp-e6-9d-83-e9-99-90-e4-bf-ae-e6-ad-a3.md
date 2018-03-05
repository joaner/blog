---
title: SELINUX下ftp权限修正
id: 103
categories:
  - linux
date: 2015-06-20 14:02:58
tags:
---

在**SELINUX**模式下（redhat系列才有），ftp无法访问home目录，即便是自己的也不行．

ftp错误: 226 Transfer done (but failed to open directory).
<pre>ftp&gt; ls
227 Entering Passive Mode (10,240,254,73,22,229).
150 Here comes the directory listing.
226 Transfer done (but failed to open directory).</pre>
有两种解决办法：

### 1\. 配置SELINUX允许访问home目录

<pre># setsebool -P allow_ftpd_full_access on
# setsebool -P ftp_home_dir on
</pre>

### 2\. 彻底关闭SELINUX，这个不太好

<pre># vi /etc/selinux/config
+ SELINUX=disabled
# reboot
</pre>
这个还需要重启机器