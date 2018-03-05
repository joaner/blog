---
title: 自签名通配符SSL证书
id: 82
categories:
  - linux
date: 2015-06-14 22:51:30
tags:
---

通配符的证书 ***.example.com** 没有免费的，看了一圈最便宜的得300块/年．不如自己用openssl签发，然后手工导入到系统的信任列表里，只是麻烦点而已．

## 创建证书

创建根证书私匙，这里的2048也可以是4096更安全
<pre>openssl genrsa -out root.key 2048</pre>
创建根证书公钥
<pre>openssl req -new -key root.key -out root.csr</pre>
创建独立的证书配置，也就是通配符域名的设置，如果想指定更多域名，可以用逗号分隔 subjectAltName=DNS:*.xiaoai.me**,DNS:*.site2.com**
<pre>echo "subjectAltName=DNS:*.xiaoai.me" &gt; cert_extensions</pre>
创建web服务器使用的CA证书
<pre>openssl x509 -req -sha512 -in root.csr -signkey root.key -extfile cert_extensions -out server.crt -days 1095</pre>
这里会询问一些参数，比如签发者的组织名，可以随便填都行

#### 参数作用

**-sha512 **加密算法，sha512是目前最安全的了，而最普遍的sha1已经开始被逐渐废弃

**-days 1095  **证书有效期3年

**-extfile cert_extensions **包含刚才创建的通配符配置

**-out server.crt** 创建服务器使用的CA证书

## 配置证书

#### nginx 主机

<pre>listen 443 ssl;
server_name ssl1.xiaoai.me;
</pre>

#### 证书配置

<pre>ssl_certificate server.crt;
ssl_certificate_key root.key;
</pre>

#### 缓存优化

<pre>ssl_session_cache shared:SSL:10m;
ssl_session_timeout 10m;</pre>

## 信任证书

现在访问 https://ssl1.xiaoai.me 会提示证书错误，需要将证书安装在系统信任根证书列表里．

#### 导出网站证书

从浏览器的查看证书选项里，导出证书．一般为 **.cer 格式 **文件

#### 导入到根证书列表里

在开始菜单中运行 **certmgr.msc** 打开证书管理器，在 **受信任的根证书颁发机构 **，右键选择导入刚才的**.cer**格式文件，最终确定信任即可．

最后需要重启下浏览器才能生效，接下来就可以看到纯绿色的https图标了