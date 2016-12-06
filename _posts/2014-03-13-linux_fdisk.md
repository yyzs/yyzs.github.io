---
layout: post
title: "每天一个linux命令(24)之fdisk"
description: "linux命令fdisk"
category: 每天一个linux命令
tags: [linux, command]
---

磁盘管理在linux中至关重要，如何多磁盘的有效管理直接关系系统的性能问题。磁盘管理用到的命令为df，du，fdisk这三个。

* df 用于检查文件系统磁盘的占用情况
* du 检查磁盘空间占用情况
* fdisk 用于磁盘分区

`fdisk`是用来对硬盘进行分区的操作。

使用`fdisk`是不需要记命令的，当你输入m的时候，命令的简介就显示出来了。

先用`df`命令查看磁盘，重点是找出磁盘的文件名

    waikeung@waikeung-laptop:~$ df
    Filesystem     1K-blocks     Used Available Use% Mounted on
    /dev/sda13      28243100   506056  26295716   2% /
    udev             1848452        4   1848448   1% /dev
    tmpfs             742660      928    741732   1% /run
    none                5120        0      5120   0% /run/lock
    none             1856644      160   1856484   1% /run/shm
    /dev/sda11       9711136  6615728   2595440  72% /opt
    /dev/sda10      95208376 43990472  46374692  49% /home
    /dev/sda12       9711136  1272712   7938456  14% /var
    /dev/sda7         136709    74304     55135  58% /boot
    /dev/sda9       38314312  3013480  33347760   9% /usr

在这里要处理的磁盘就是sda。


分区磁盘是管理员才有权利的，所以要用管理员权限才能运行。

    root@debian:~# fdisk /dev/sda
    
    Command (m for help):           <-- 等待输入
    
输入m会显示命令的介绍

    Command (m for help): m         <-- 输入m显示help
    Command action
       a   toggle a bootable flag  
       b   edit bsd disklabel
       c   toggle the dos compatibility flag
       d   delete a partition       <-- 删除一个分区
       l   list known partition types
       m   print this menu
       n   add a new partition      <-- 新增一个分区
       o   create a new empty DOS partition table
       p   print the partition table    <-- 显示分区表
       q   quit without saving changes  <-- 退出程序，不存储修改
       s   create a new empty Sun disklabel
       t   change a partition's system id
       u   change display/entry units
       v   verify the partition table
       w   write table to disk and exit <-- 将操作写入分区表
       x   extra functionality (experts only)
    
在`fdisk`无论做什么样的操作，只要输入`q`就会退出，所有的操作都不会生效，但当你按下`w`时，就是操作生效的意思。

查看分区表

    Command (m for help): p
    
    Disk /dev/sda: 8589MB, 8589934592 bytes
    255 heads, 63 sectors/track, 1044 cylinders, total 16777216 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 4096 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0x0004cba2
    
       Device Boot      Start         End      Blocks   Id  System
    /dev/sda1   *        2048    15988735     7993344   83  Linux
    /dev/sda2        15990782    16775167      392193    5  Extended
    /dev/sda5        15990784    16775167      392192   82  Linux Swap / Solaris

删除分区

    Command (m for help): p
    Partition number (1-5): 5   <-- 输入分区编号

这样/dev/sda5这个分区就删除了。

新增分区

    Command (m for help): n
    Command action
       e   extended
       p   primary partition (1-4)
    Select (default p): p
    Partiton number(1-4, default 3): 3
    First sector (15988736-16777215, default 15988736): 
    Using default value 15988736
    Last sector, +sectors or +size{K,M,G} (15988736-15990781, default 15990781):
    Using default value 15990781

这里要选择新建的分区类型，是主分区还是扩展分区；并选择p或是e。然后就是设置分区的大小。

最后面，特别提醒：刚才所有的操作，要生效要按下`w`。如果不想生效，不保存，按下`q`，退出。
