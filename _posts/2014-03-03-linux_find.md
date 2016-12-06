---
layout: post
title: "每天一个linux命令(16)之find"
description: "linux命令find"
category: 每天一个linux命令
tags: [linux, command]
---

`find`是非常强大的搜索命，配合上`grep`，大大提高了工作效率。

`find`可以在目录及其子目录中搜索文件，可以指定匹配条件，比如按文件名、文件类型、用户、还有时间戳等来查找文件。

##格式

```sh
find [PATH] [option] [action]
```

##例子和参数
###与文件名称和权限有关的参数

* -name filename 查找文件名为filename的文件
* -size [+-] SIZE 查找比SIZE还要大(+)或小(-)的文件
* -type TYPE 查找文件类型为TYPE的文件,类型主要有：普通文件(f)、目录(d)、符号链接文件(l)、管道文件(p)、块设备文件(b)、字符设备文件(c)、socket(s)等。
* -perm 按照文件的权限来查找文件

1. 用文件名来查找文件


        waikeung@waikeung-laptop:~$ find -name test
        ./Code/test
        ./Code/test/test

加上`-i`忽略大小写

2. 找出/dev目录下文件类型为字符设备的文件名有哪些

        waikeung@waikeung-laptop:/$ find /dev -type c
        /dev/vboxnetctl
        /dev/vboxdrv
        /dev/fb1
        /dev/fb0
        /dev/video0
        /dev/dri/card1
        /dev/dri/controlD65
        /dev/dri/card0
        /dev/dri/controlD64
        /dev/vcsa4
        ...(以下省略)...

3. 查找所有隐藏的目录`find -type d -name ".*"`
4. 查找所有隐藏的文件`find -type f -name ".*"`
5. 找出home目录及子目录下所有的空文件(0字节文件) `find ~ -empty`
6. 通过文件大小查找
    1. 查找比指定文件大的文件 `find -size +100M`
    2. 查找比指定文件小的文件 `find -size -100M`
    3. 查找符合文件大小的文件 `find -size 100M`

7. 查找当前目录下文件权限为777的文件，即rwxrwxrwx `find -perm 777 -exec ls -l {} \; `

###与用户和用户组有关的参数

* -user name 查找文件拥有者为name的文件
* -group name 查找文件所属用户组名为name的文件
* -nouser 查找无有效拥有者的文件，即该文件的拥有者在/etc/passwd中不存在
* -nogroup 查找无有效所属用户组的文件，即该文件的所属用户组在/etc/group中不存在


###与时间相关的参数

与时间有关的参数是`-atime`、`-ctime`、`-mtime`。下面以`-mtime`为例:

用`-`限定更改时间在距今n日以内的文件，用`+`来限定更改时间在距今n日以前的文件，没用+—就限定更改时间在距今n天前那天修改的文件。

* -newer file 列出比file还要新的文件

1. 在系统根目录下查找更改时间在3天以内的文件，`find / -mtime -3`
2. 查找根目录下昨天修改的文件，`find / -mtime 1`
3. 寻找/etc目录下，比/etc/passwd还要新的文件 `find /etc -newer /etc/passwd`。**-newer用在分辨两个文件新旧关系很有用**

###其他参数

* -exec 后面接其他命令，用来处理查找到的结果
* -print 将结果打印在屏幕上，默认操作

##参考

1. [妈咪，我找到了！--15个实用的Linux find命令](http://www.oschina.net/translate/15-practical-linux-find-command-examples)
2. 鸟哥的Linux私房菜
