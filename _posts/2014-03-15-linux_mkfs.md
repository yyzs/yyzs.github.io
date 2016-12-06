---
layout: post
title: "每天一个linux命令(25)之mkfs"
description: "linux命令mkfs"
category: 每天一个linux命令
tags: [linux, command]
---

`fdisk`是给磁盘分区的，分区完毕后自然就是要进行文件系统的格式化。格式化的命令就是`mkfs`，`make file system`。

`mkfs`其实十个综合命令，比如当执行`mkfs -t ext4...`时,系统会去调用`mkfs.ext4`这个命令来完成格式化操作。

##格式

```sh
mkfs [-t 文件系统格式] 设备文件名
```
* -t fstype fstype是要创建的文件系统的类型，如ext3, ext4等
* -V 显示更多输出，包括文件系统的相关信息

列出本地系统上创建文件系统的程序 `ls /sbin/mkfs.*`

    waikeung@waikeung-laptop:~$ ls /sbin/mkfs.*
    /sbin/mkfs.bfs     /sbin/mkfs.ext2  /sbin/mkfs.ext4     /sbin/mkfs.minix  /sbin/mkfs.ntfs
    /sbin/mkfs.cramfs  /sbin/mkfs.ext3  /sbin/mkfs.ext4dev  /sbin/mkfs.msdos  /sbin/mkfs.vfat

##例子

1. 格式化为fat32格式 `mkfs -t vfat /dev/sdb1`
2. 创建文件系统，并显示信息，此时-V必须放在-t前面 `mkfs -V -t vfat /dev/sdb1`
3. 用特定选项创建文件系统，比如要创建为ntfs格式的文件系统 `mkfs.ntfs /dev/sdb1`

PS:

1. 创建文件系统需要root权限
2. `fdisk -l`可以查看文件系统，该命令也需要root权限
