---
layout: post
title: "每天一个linux命令(5)之rmdir"
description: "linux命令rmdir"
category: 每天一个linux命令
tags: [linux, command]
---

`rmdir`命令是删除一个空目录。一个目录被删除之前必须是空的，删除目录必须对其父目录具有写的权限。

##格式
```sh
rmdir [参数] 目录
```

##参数

* -p
      递归的删除目录，当子目录删除后父目录为空时，也删除父目录。
* -v,  --verbose
      显示指令执行过程

##例子
* 删除一个空目录 `rmdir dir1`
* rmdir不能删除非空目录

        waikeung@waikeung-ThinkPad:/tmp$ rmdir test
        rmdir: failed to remove `test': Directory not empty

