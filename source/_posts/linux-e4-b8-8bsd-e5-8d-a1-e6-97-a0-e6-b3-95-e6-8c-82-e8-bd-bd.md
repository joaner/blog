---
title: linux下SD卡无法挂载
id: 147
categories:
  - linux
date: 2015-07-30 22:50:41
tags:
---

这两天用SD卡给树莓派制作系统，但一直没成功，插入SD卡灯也不闪烁，总是亮着。

而烧录好镜像后的SD卡，无法再挂载在linux上了，我的系统是Fedora22，能听到插入的系统提示音，但无法看到SD卡的文件系统。所以只好格式化SD卡：

1\. 找到SD卡的设备：
```
sudo fdisk -l
```
按照容量我的SD卡在 **/dev/sdb**

2\. 格式化为FAT32类型，这是所有系统通用的
```
sudo mkdosfs -F 32 -I /dev/sdb
```
完成后重新插拔即可看到SD卡的文件系统。

接下来又反复折腾。。。