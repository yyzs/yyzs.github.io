---
layout: post
title: "每天一个linux命令(13)之tail"
description: "linux命令tail"
category: 每天一个linux命令
tags: [linux, command]
---

`head`用来显示文件的开头，而`tail`是用来显示文件末尾的，显示到标准输出。默认显示10行。常用来查看日志文件。

##格式

```sh
tail [参数] [文件]
```

##参数

* -f 循环读取
* -q, --quiet, --silent 不显示文件名
* -v, --verbose 显示文件名
* -c, --bytes=k 显示字节数
* -n, --lines=k 显示行数
* --pid=PID 与-f合用,表示在进程ID,PID死掉之后结束. 
* -s, --sleep-interval=N 与-f合用,表示在每次反复的间隔休眠N秒 

##示例

1. 显示文件后5行 `tail -n 5 file`
2. 从第5行开始显示文件 `tail -n -5 file`
3. 循环查看文件内容

        [root@localhost ~]# ping 192.168.120.204 > test.log &
        [1] 11891
        [root@localhost ~]# tail -f test.log 
        PING 192.168.120.204 (192.168.120.204) 56(84) bytes of data.
        64 bytes from 192.168.120.204: icmp_seq=1 ttl=64 time=0.038 ms
        64 bytes from 192.168.120.204: icmp_seq=2 ttl=64 time=0.036 ms
        64 bytes from 192.168.120.204: icmp_seq=3 ttl=64 time=0.033 ms
        64 bytes from 192.168.120.204: icmp_seq=4 ttl=64 time=0.027 ms
        64 bytes from 192.168.120.204: icmp_seq=5 ttl=64 time=0.032 ms
        64 bytes from 192.168.120.204: icmp_seq=6 ttl=64 time=0.026 ms
        64 bytes from 192.168.120.204: icmp_seq=7 ttl=64 time=0.030 ms
        64 bytes from 192.168.120.204: icmp_seq=8 ttl=64 time=0.029 ms
        64 bytes from 192.168.120.204: icmp_seq=9 ttl=64 time=0.044 ms
        64 bytes from 192.168.120.204: icmp_seq=10 ttl=64 time=0.033 ms
        64 bytes from 192.168.120.204: icmp_seq=11 ttl=64 time=0.027 ms
        [root@localhost ~]#
