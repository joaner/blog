---
title: 折腾Let‘s Encrypt免费SSL证书
id: 223
categories:
  - linux
date: 2015-12-04 23:28:27
tags:
---

今天开始折腾Let’s Encrypt的免费SSL证书。

> [https://letsencrypt.org/howitworks/](https://letsencrypt.org/howitworks/)

通过一行命令，它会自动完成SSL的域名认证，证书生成等工作，最重要的是免费。虽然WoSign,StartSSL也免费，不过申请有点不方便。

首先，在网站DNS指向的机器上，执行创建证书的命令。比如`xiaoai.me`指向`104.155.200.103`，那么只有在`104.155.200.103`上才能调用命令创建。

    $ git clone https://github.com/letsencrypt/letsencrypt
    $ ./letsencrypt/letsencrypt-auto certonly -d xiaoai.me
    `</pre>

    任何厂商都需要确保证书不会被冒领，因此Let‘s Encrypt会首先验证你是网站域名拥有者。在执行以上命令时，他们的远程服务器会请求申请的网站，通过预先放置的特殊文件等手段来确认身份。

    但是这一步遇到了DNSpod在海外（Let‘s Encrypt的服务器）解析超时的问题。要解决这个问题，只能放弃DNSpod，没有其它办法。

    > Failed authorization procedure. xiaoai.me (tls-sni-01): urn:acme:error:connection :: The server could not connect to the client for DV :: DNS query timed out

    > 许多人反映DNSpod有问题：https://github.com/letsencrypt/letsencrypt/issues/1610

    我把域名服务器换到google cloud DNS，解决了DNS的问题。

    再次运行，填写联系邮箱，同意协议，接下来就成功了。注意在运行命令前需要先关闭占用`80`端口的服务器。

    > IMPORTANT NOTES:
>        - Congratulations! Your certificate and chain have been saved at /etc/letsencrypt/live/xiaoai.me/fullchain.pem. Your cert will expire on 2016-03-03\. To obtain a new version of the certificate in the future, simply run Let's Encrypt again.
>        - If like Let's Encrypt, please consider supporting our work by:
>          Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
>          Donating to EFF:                    https://eff.org/donate-le

    脚本已经自动生成了一套SSL证书，其位置如下：

    <pre>`$ sudo ls /etc/letsencrypt/live/xiaoai.me
    cert.pem  chain.pem  fullchain.pem  privkey.pem
    `</pre>

    ### 配置

    接下来把证书添加到nginx的配置文件中就OK了：

    <pre>`ssl on;
    listen  443 ssl http2;
    server_name xiaoai.me
    ssl_certificate /etc/letsencrypt/live/xiaoai.me/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/xiaoai.me/privkey.pem;
    `</pre>

    整个过程非常简单，现在网站的证书颁发机构变成了 `Let's Encrypt Authority X1`

    ### 说明

    #### let's encrypt 提供了多种验证方式：

*   上面关闭80端口的服务，由创建脚本暂时监听`xiaoai.me:80`端口，以验证网站属于创建者
*   通过 `--webroot` 参数指定网站根目录，创建脚本会在目录里放置验证文件，用以让远程服务器来访问
*   通过 `--apache` 参数，自动干预apache服务器，验证原理和上面类似
*   不需要也不支持验证网站主的邮箱

    ### 进阶

    #### 多域名

    可以对多个域名生成单个证书，添加多个`-d`参数即可：

    <pre>`./letsencrypt-auto certonly -d xiaoai.me -d www.xiaoai.me -d joaner.com
    `</pre>

    但是不支持通配符的域名 `*.xiaoai.me`

    > Wildcard domains are not supported

    #### 有效期

    Let’s Encrypt的证书有效期只有**90天**，不过可以设置一个crontab任务自动续期证书

    <pre>`/path/to/letsencrypt-auto certonly -d xiaoai.me --renew --agree-dev-preview
    nginx -s reload
    