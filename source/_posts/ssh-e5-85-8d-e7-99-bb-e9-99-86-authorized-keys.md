---
title: SSH 免登陆 authorized_keys
id: 33
categories:
  - linux
date: 2015-05-09 05:28:26
tags:
---

### authorized_keys

这是通用的做法，将本地机器的 `id_rsa.pub` 公匙字符串拷贝下来，追加到服务器的 `~/.ssh/authorized_keys` 文件下，作为一行,并且保持其它人不可写的权限,就可以不用再输入密码．

#### 客户端的配置

##### linux

创建客户端ssh key,回车选默认的即可
创建程序会询问你的key存放位置,以及加密密码等,回车选则默认即可
<pre>$ ssh-keygen
</pre>
打印认证公匙,复制然后开始下一步
<pre>$ cat ~/.ssh/id_rsa.pub
</pre>
公匙 ~/.ssh/id_rsa.pub 是每次登录ssh时都会发送的,因而把其加入到服务器的 ~/.ssh/authorized_keys 后,服务器会据此判断是否可以免除密码认证过程

&nbsp;

#### 服务端的配置

创建自动认证文件,如果没有的话
<pre>$ touch ~/.ssh/authorized_keys</pre>
设置该文件权限为其它人不可写,权限限制是必须的,否则将不会生效
<pre>$ chmod 644 ~/.ssh/authorized_keys</pre>
将在客户端拷贝的 ~/.ssh/id_rsa.pub 内容追加到认证文件,它只占一行
<pre>$ vi ~/.ssh/authorized_keys</pre>