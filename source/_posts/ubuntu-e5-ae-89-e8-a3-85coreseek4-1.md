---
title: ubuntu安装coreseek4.1
id: 27
categories:
  - sphinx
date: 2014-07-11 16:36:10
tags:
---

目录规划：

此过程将编译两个包， mmseg-3.2.14 和 coreseek-4.1，它们都将包含版本号放置在 /server 下

索引和日志放在 /alidata/coreseek/ 下

&nbsp;

下载源码包，包里集成了coreseek4.1 mmseg3.1.4
<pre class="lang:sh decode:true">wget http://www.coreseek.cn/uploads/csft/4.0/coreseek-4.1-beta.tar.gz</pre>
&nbsp;

安装libtool (coreseek需要)
<pre class="lang:sh decode:true ">apt-get install libtool</pre>
&nbsp;

实际安装: libltdl-dev libltdl7 libtool

安装mysql开发者包
<pre class="lang:default decode:true ">sudo apt-get install mysql-devel</pre>
&nbsp;

实际安装: libmysqlclient-dev libmysqlclient18 libmysqld-dev mysql-common

&nbsp;

安装 mmseg  (源码集成在 coreseek-4.1-beta)
<pre class="lang:sh decode:true ">cd  mmseg-3.2.14/

# 包内自带的编译前初始化工具

./bootstrap 

# 安装到 /server/mmseg-3.2.14

./configure --prefix=/server/mmseg-3.2.14

make &amp;&amp; make install</pre>
&nbsp;

安装 coreseek
<pre class="lang:sh decode:true"># 包内自带的编译前初始化工具
./buildconf.sh
./configure --prefix=/server/coreseek-4.1 --with-mysql --with-mmseg --with-mysql-includes=/usr/include/mysql --with-mmseg-includes=/server/mmseg-3.2.14/include/mmseg --with-mmseg-libs=/server/mmseg-3.2.14/lib
make &amp;&amp; make install</pre>
-------------------- 安装完成 ---------------

创建索引数据与日志的目录：
<pre class="lang:sh decode:true ">mkdir /alidata/coreseek
mkdir /alidata/coreseek/index
mkdir /alidata/coreseek/log</pre>
&nbsp;

&nbsp;

添加配置文件
<pre class="lang:sh decode:true "># 从 xueba-tiku 仓库中提取sphinx的配置文件

ln -s /home/pxk/www/xueba-tiku/protected/data/sphinx.conf  /server/coreseek-4.1/etc/

# 修改配置 /server/coreseek-4.1/etc/sphinx.conf 中的路径和数据库参数

# 添加词库 （词库在仓库里）

/server/mmseg-3.2.14/bin/mmseg -u /home/pxk/www/xueba-tiku/protected/data/edu.txt

# 添加stopword.txt （忽略词汇）

ln -s /home/pxk/www/xueba-tiku/protected/data/stopwords.txt /server/coreseek-4.1/etc/</pre>
&nbsp;

&nbsp;

--------------------------- 配置完成 ---------------------------
<pre class="lang:sh decode:true "># 创建索引数据

/server/coreseek-4.1/bin/indexer --config /server/coreseek-4.1/etc/sphinx.conf --all

# 启动搜索服务后台进程

/server/coreseek-4.1/bin/searchd --config /server/coreseek-4.1/etc/sphinx.conf</pre>
&nbsp;

&nbsp;