---
layout: post
title: "每天一个linux命令(4)之mkdir"
description: "linux命令mkdir"
category: 每天一个linux命令
tags: [linux, command]
---

linux中创建指定名称的目录。要求创建目录的用户在当前目录中具有写权限，同时指定的目录名不能是当前目录中已有的目录。

##格式
```sh
mkdir [参数] 目录
```
##参数

* -m, --mode=MODE
      set file mode (as in chmod), not a=rwx - umask
* -p, --parents
      可以是一个路径名称，若此时路径中有某些目录不存在，系统会自动建立那些还不存在的目录。
* -v, --verbose
      创建新目录时显示信息

##例子

* 创建一个空目录 `mkdir test`
* 递归创建多个目录

        waikeung@waikeung-ThinkPad:/tmp$ mkdir -p test/test1
        waikeung@waikeung-ThinkPad:/tmp$ cd test/test1/
        waikeung@waikeung-ThinkPad:/tmp/test/test1$ 

* 创建权限为777的目录

        waikeung@waikeung-ThinkPad:/tmp/test$ mkdir -m 777 a
        waikeung@waikeung-ThinkPad:/tmp/test$ ls -l
        total 8
        drwxrwxrwx 2 waikeung waikeung 4096 Feb  5 20:10 a

