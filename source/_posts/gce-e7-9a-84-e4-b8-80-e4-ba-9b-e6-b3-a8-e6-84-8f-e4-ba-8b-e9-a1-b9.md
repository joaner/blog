---
title: GCE的一些注意事项
id: 137
categories:
  - linux
date: 2015-07-22 00:03:29
tags:
---

GCE用了几个月，还算不错，挺稳定的．我也遇到几个问题，整理出来：

## 无法发送邮件

这个没办法，google封锁了出口流量中的25等邮件端口（还包括SSL邮件端口）．能收取，但不能向外网主机发送邮件．

官方说明: [https://cloud.google.com/compute/docs/tutorials/sending-mail/](https://cloud.google.com/compute/docs/tutorials/sending-mail/) 解决办法也在这文章下面

## 控制台绑定硬盘不生效

还需要手动挂载到系统目录，和阿里云一样．先确认是否绑定成功，查看当前硬盘列表:
```
$ ls -l /dev/disk/by-id/
lrwxrwxrwx. 1 root root  9 Jun 14 03:30 /dev/disk/by-id/google-instance-3 -> ../../sda
lrwxrwxrwx. 1 root root 10 Jul 21 23:10 /dev/disk/by-id/google-instance-3-part1 -> ../../sda1
lrwxrwxrwx. 1 root root  9 Jul 21 23:27 /dev/disk/by-id/google-persistent-disk-1 -> ../../sdb
lrwxrwxrwx. 1 root root  9 Jun 14 03:30 /dev/disk/by-id/scsi-0Google_PersistentDisk_instance-3 -> ../../sda
lrwxrwxrwx. 1 root root 10 Jul 21 23:10 /dev/disk/by-id/scsi-0Google_PersistentDisk_instance-3-part1 -> ../../sda1
lrwxrwxrwx. 1 root root  9 Jul 21 23:27 /dev/disk/by-id/scsi-0Google_PersistentDisk_persistent-disk-1 -> ../../sdb

```
OK，可以看到 **/dev/disk/by-id/google-persistent-disk-1** 就是新创建的硬盘 **disk-1**

现在需要在机器中手动挂载，比如硬盘20G **disk-1** 在 **/dev/disk/by-id/google-persistent-disk-1**，想挂载到 **/data**
```
$ sudo /usr/share/google/safe_format_and_mount -m "mkfs.ext4 -F" /dev/disk/by-id/google-persistent-disk-1 /data
safe_format_and_mount: Running: fsck.xfs -a /dev/disk/by-id/google-persistent-disk-1
safe_format_and_mount: /sbin/fsck.xfs: XFS file system.
safe_format_and_mount: Running: mount -o discard,defaults /dev/disk/by-id/google-persistent-disk-1 /data
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        10G  7.2G  2.9G  72% /
devtmpfs        1.9G     0  1.9G   0% /dev
tmpfs           1.9G     0  1.9G   0% /dev/shm
tmpfs           1.9G  193M  1.7G  11% /run
tmpfs           1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/sdb         20G   45M   19G   1% /data

```
可以看到生效了

详细文档：[https://cloud.google.com/compute/docs/disks/persistent-disks#attach_disk](https://cloud.google.com/compute/docs/disks/persistent-disks#attach_disk)

## 执行**gcloud**命令遇到 Insufficient Permission 错误

这是gcloud配置还没有认证
```
$ sudo /usr/local/bin/gcloud auth login

...  这里会给出一个授权网页链接，机器的账户授权后会给一个verification code

Enter verification code: 

```
&nbsp;

暂时就这些．总的来说挺喜欢GCE的，而且前3个月免费哦!