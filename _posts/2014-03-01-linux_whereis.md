---
layout: post
title: "每天一个linux命令(14)之whereis"
description: "linux命令whereis"
category: 每天一个linux命令
tags: [linux, command]
---

如果要在查找文件，该用什么命令，`whereis`，`locate`，`find`都是可以用来查找文件的。`whereis`和`locate`都是通过数据库来查找数据的，速度快，而`find`是通过实际查询硬盘。这里先介绍`whereis`命令。

`whereis`使用来查找特定的文件，比如二进制文件，manual路径下的文件，查找source源文件等。

##格式

```sh
whereis [参数] 文件或目录
```

##参数

* -b 只找二进制格式的文件
* -m 只找在说明文件manual路径下的文件
* -s 只找source源文件
* -u 查找不在上述三个选项当中的其他特殊文件

##例子

1. 查看与passwd有关的说明文件文件名

        waikeung@waikeung-laptop:~$ whereis -m passwd
        passwd: /usr/share/man/man5/passwd.5.gz /usr/share/man/man1/passwd.1.gz /usr/share/man/man1/passwd.1ssl.gz

2. 查找ifconfig

        waikeung@waikeung-laptop:~$ whereis ifconfig
        ifconfig: /sbin/ifconfig /usr/share/man/man8/ifconfig.8.gz

