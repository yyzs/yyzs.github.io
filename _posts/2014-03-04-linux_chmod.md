---
layout: post
title: "每天一个linux命令(17)之chmod"
description: "linux命令chmod"
category: 每天一个linux命令
tags: [linux, command]
---

当我在处理处理一些文件什么的时候，偶尔屏幕上就会出现"Permission deny"，这都是权限的设置的问题。

linux上的文件权限有读、写、执行，对应的就是rwx。linux的系统中，每个文件、目录都有对应的访问权限，用来确认谁可以通过何种方式对文件或目录进行访问和操作。

每一个文件或目录都有三组访问权限，每组三位，分别对应rwx。用`ls -l`命令可以显示文件或目录的详细信息。


    waikeung@waikeung-laptop:~$ ls -l
    total 60
    drwxrwxr-x  2 waikeung waikeung 4096 Feb 28 22:59 bin
    drwxrwxr-x  8 waikeung waikeung 4096 Mar  4 10:54 Code
    drwxr-xr-x  2 waikeung waikeung 4096 Mar  4 10:33 Desktop
    drwxr-xr-x  6 waikeung waikeung 4096 Mar  2 21:34 Documents
    drwxr-xr-x  4 waikeung waikeung 4096 Mar  4 10:59 Downloads
    drwx------ 13 waikeung waikeung 4096 Mar  4 09:52 Dropbox
    ...后面省略...

输出的最左边一栏，比如`drwxrwxr-x`,有10个位置，第一个字符代表文件的类型，目录也可被认为是一个文件。如果第一个字符是d，表示是个目录文件，如果是-，表示是个非目录文件，如果是l，表示链接文件等。第2个字符到第10个字符，3个字符一组，代表用户对文件的权限。第一组为文件所有者的权限，第二组为同用户组的权限，第三组为其他用户的权限。

`chmod`命令是用来修改文件的权限的，权限的设置可以用数字或符号来表示。

##格式

```sh
chmod [-Rcv] mode file
```
##参数

* -R 递归，连同子目录下的所有文件都会更改
* -c 当发生改变时，报告处理信息
* -v 运行时显示详细处理信息

##数字设定法
权限代号：

* r: 4
* w: 2:
* x: 1

每种身份有三个权限rwx，需要累加，比如 `-rwxrwxr-x`，分数为:

* u = rwx = 4+2+1 = 7
* g = rwx = 4+2+1 = 7
* o = r-x = 4+0+1 = 5

要设定权限为`rwxrwxr-x`时，权限数字就为775。

`chomd xyz file`


    waikeung@waikeung-laptop:~$ ls -al .bashrc
    -rw-r--r-- 1 waikeung waikeung 3659 Feb 21 00:17 .bashrc
    waikeung@waikeung-laptop:~$ chmod 777 .bashrc
    waikeung@waikeung-laptop:~$ ls -al .bashrc
    -rwxrwxrwx 1 waikeung waikeung 3659 Feb 21 00:17 .bashrc

如果有些不想让别人看到的文件，可以将文件权限设定为`rwx------`，命令为`chmod 700 filename`。

##文字设定法
权限范围：

* u: 文件的拥有者
* g: 文件的当前用户群组
* o: 其他用户
* a: 所有用户

`chmod [who] [+|-|=] [mode] file`

* \+ 加入
* \- 除去
* = 设置

将.bashrc设定为rwxr-xr-x:

    waikeung@waikeung-laptop:~$ ls -al .bashrc
    -rw-r--r-- 1 waikeung waikeung 3659 Feb 21 00:17 .bashrc
    waikeung@waikeung-laptop:~$ chmod u=rwx,go=rx .bashrc
    waikeung@waikeung-laptop:~$ ls -al .bashrc
    -rwxr-xr-x 1 waikeung waikeung 3659 Feb 21 00:17 .bashrc

##例子

1. 增加所有用户可执行权限 `chmod a+x filename`
2. 给刚写的sh文件加上执行权限 `chmod +x test.sh`

        waikeung@waikeung-laptop:~$ ls -l test.sh 
        -rw-rw-r-- 1 waikeung waikeung 346 Mar  4 12:40 test.sh
        waikeung@waikeung-laptop:~$ chmod +x test.sh 
        waikeung@waikeung-laptop:~$ ls -l test.sh
        -rwxrwxr-x 1 waikeung waikeung 346 Mar  4 12:40 test.sh

3. 删除可执行权限 `chmod a-x filename`
4. 对一个目录及子目录下所有文件修改权限 `chmod -R u+x dir`
5. 给文件设定775(rwxrwxr-x)权限 `chmod 775 filename`
