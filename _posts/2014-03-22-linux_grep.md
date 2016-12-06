---
layout: post
title: "每天一个linux命令(27)之grep"
description: "linux命令grep"
category: 每天一个linux命令
tags: [linux, command]
---

linux中grep命令是个强大的文本搜索工具，能使用正则表达式搜索文本，并把匹配的结果行打印出来。

`grep`就是解析一行文字，取得关键字，若该行有关键字，就会整行列出来。

##格式

```sh
grep [option] pattern file
```

##参数

* -c 计算找到 pattern 的次数
* -i 忽略大小写的差异
* -n 显示匹配行及行号
* -v 反向选择，显示没有匹配的行
* -h, --no-filename 查询多文件时，不显示文件名
* -l, --files-with-matches 显示匹配的文件名

要用好grep这个工具，实际上就是要熟悉正则表达式，在这里不对正则表达式详细介绍，后面会用一篇文章来说明。

##pattern 样例

* ^word     待查找的字符串word在行首
* word$     待查找的字符串word在行尾
* .         匹配一个非换行的字符
* \*        匹陪前一个字符零次或多次
* \\        转义字符
* [list]    匹配指定字符集中的一个字符
* [a-z]     匹配一个指定范围内的字符
* [^list]   匹配一个不在指定范围内的字符

##例子

1. 简单例子

        waikeung@waikeung-laptop:~$ cat /etc/passwd | grep 'root'
        root:x:0:0:root:/root:/bin/bash

    `grep`命令可以配合上管道使用。

2. 通过管道过滤ls -l输出的内容，只显示以a开头的行。

        ls -l | grep '^a'

3. 在文件file1,file2,file3中查找有test的行

        grep 'test' file1 file2 file3

4. 列出啊/etc/下面文件类型为连接文件属性的文件名

        ls -l | /etc | grep '^|'

