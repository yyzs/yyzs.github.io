---
layout: post
title: "每天一个linux命令(12)之head"
description: "linux命令head"
category: 每天一个linux命令
tags: [linux, command]
---

`head`用来显示文件的开头，显示到标准输出。默认显示10行。

##格式

```sh
head [参数] [文件]
```

##参数

* -c, --bytes=[-]k 显示字节数
* -n, --lines=[-]k 显示行数
* -q, --quiet, --silent 不显示文件名
* -v, --verbose 显示文件名

##示例

1. 显示文件前5行 `head -n 5 file`
2. 显示文件前32个字节 `head -c 32 file`
3. 显示文件除了最后5行 `head -n -5 file`
4. 显示文件除了最后32个字节 `head -c -32 file`
