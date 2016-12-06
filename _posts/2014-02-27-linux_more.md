---
layout: post
title: "每天一个linux命令(10)之more"
description: "linux命令more"
category: 每天一个linux命令
tags: [linux, command]
---

上一个命令`cat`是把文件的内容都全部显示出来，如果超过一个屏幕了，这就有些麻烦了。这里再介绍一个显示文件的命令`more`，与`cat`不同的是，`more`命令是一页一页显示的，通过空格键(space)就可以前往下一页。b键前往上一页，但这前往上一页只对文件有效，对管道无效。同时按页查看文件时，还支持直接搜索字符串的功能。

##格式

```sh
more [参数] [文件]
```

##参数

* +n 从第n行开始显示
* -n 定义屏幕大小为n行
* -c 清屏，然后开始显示
* -d 显示提示"Press space to continue，’q’ to quit"，同时禁用响铃
* -p 通过清除窗口而不是滚屏来对文件进行换页
* -s 把连续的多个空行显示为一行

##常用操作命令

    /string   查找string
    Enter     向下滚动n行，需要定义，默认为1行
    Space     向下滚动一屏
    =         输出当前行号
    b         返回上一屏
    :f        输出当前文件名和当前行号
    V         调用vi编辑器
    !cmd     调用shell，并执行cmd
    q         退出more

##例子

1. 显示文件中第5行起的内容 `more +5 file`
2. 配合管道适用

        waikeung@waikeung-ThinkPad:/$ ls -l | more -5
        total 87
        drwxr-xr-x   2 root root  4096 Jan 17 10:08 bin
        drwxr-xr-x   4 root root  3072 Jan 17 10:12 boot
        drwxr-xr-x   2 root root  4096 Dec 10 18:00 cdrom
        drwxr-xr-x  18 root root  4460 Feb  9 07:32 dev
        --More--

