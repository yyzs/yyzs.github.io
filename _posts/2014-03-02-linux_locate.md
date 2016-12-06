---
layout: post
title: "每天一个linux命令(15)之locate"
description: "linux命令locate"
category: 每天一个linux命令
tags: [linux, command]
---

另一个通过数据库来查找的命令是`locate`。`locate`的使用很简单，后面接要搜索的文件的部分名称，就能得到结果了。

几个注意的地方：
* locate 后面接文件部分名称就能得到结果，比如输入passwd，只要文件名里有passwd在里面就会被显示出来。如果忘记某个文件的完整文件名，可以试试这个命令。
* locate 是通过`/var/lib/mlocate/`里面的数据所查找的，不需要在硬盘中直接访问数据，速度快。但也有一个问题，这个数据库一般每天更新一次，有时查找会找不到文件，这个时候就需要手动更新数据库了，更新的命令是`updatedb`。

##格式

```sh
locate [-ir] keyword
```

##参数

* -i 忽略大小写差异
* -r 后面接正则表达式的显示方法

##例子

1. 查找文件名包括passwd的文件

        waikeung@waikeung-laptop:~$ locate passwd
        /etc/passwd
        /etc/passwd-
        /etc/cron.daily/passwd
        /etc/init/passwd.conf
        /etc/init.d/passwd
        /etc/pam.d/chpasswd
        /etc/pam.d/passwd
        /etc/security/opasswd
        ...(下面省略)...

