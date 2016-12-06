---
layout: post
title: "每天一个linux命令(21)之which"
description: "linux命令which"
category: 每天一个linux命令
tags: [linux, command]
---

`which`是通过PATH变量指定的路径来查找可执行文件的。

##格式

```sh
which 可执行文件名
```

##参数

* -a 将所有找到的命令均列出来，而不是第一个被找到的命令 

##例子

1. 找到ifconfig的位置

        waikeung@waikeung-laptop:~$ which ifconfig 
        /sbin/ifconfig

2. 查找cd命令的位置

        waikeung@waikeung-laptop:~$ which cd
        waikeung@waikeung-laptop:~$ 

找不到cd命令，为什么？cd是bash的内置命令，而which找的是PATH变量内的路径，当然找不到。
