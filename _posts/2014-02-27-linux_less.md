---
layout: post
title: "每天一个linux命令(11)之less"
description: "linux命令less"
category: 每天一个linux命令
tags: [linux, command]
---

同`more`，`cat`一样，`less`也是查看文件的，对文件分页显示的工具。但同`more`相比，`less`更加强大。`less`能随意浏览文件，还能向前向后搜索。

##格式

```sh
less [参数] 文件
```

##参数

* -b <缓冲区大小> 设置缓冲区的大小
* -e 当文件显示结束后，自动离开
* -f 强迫打开特殊文件，例如外围设备代号、目录和二进制文件
* -g 只标志最后搜索的关键词
* -i 忽略搜索时的大小写
* -m 显示类似more命令的百分比
* -N 显示每行的行号
* -o <文件名> 将less 输出的内容在指定文件中保存起来
* -Q 不使用警告音
* -s 显示连续空行为一行
* -S 行过长时间将超出部分舍弃
* -x <数字> 将“tab”键显示为规定的数字空格

##常用操作命令

    /string   向下搜索“string”的功能
    ?string   向上搜索“string”的功能
    n         重复前一个搜索（与 / 或 ? 有关）
    N         反向重复前一个搜索（与 / 或 ? 有关）
    b         向后翻一页
    d         向后翻半页
    h         显示帮助界面
    q         退出less 命令
    u         向前滚动半页
    y         向前滚动一行
    空格键    滚动一行
    回车键    滚动一页
    [pagedown]   向下翻动一页
    [pageup]     向上翻动一页

##例子

1. 查看文件 `less file`
2. ps查看进程用less分页显示 `ps aux | less`

更多用法查看 `man less`
