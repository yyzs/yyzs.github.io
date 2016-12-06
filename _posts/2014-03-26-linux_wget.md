---
layout: post
title: "每天一个linux命令(29)之wget"
description: "linux命令cut"
category: 每天一个linux命令
tags: [linux, command]
---

`wget`是linux中一个下载文件的命令，用于命令行中。`wget`支持http、https和ftp协议，也可以使用http代理。

同时`wget`也可以递归的下载html页面的链接，也就是说可以创建远程服务器的本地版本，完整重建原始站点的目录结构，方便离线浏览。

##格式

```sh
wget [参数] [URL地址]
```

##参数

这里的参数都是一些常用的，具体的参数请`man wget`。

 * -b, --background 启动后转入后台执行
 

###记录和输入文件参数

 * -o, --output-file=FILE 把记录写入到FILE文件中
 * -a, --append-output=FILE 把记录追加到FILE文件中
 * -d, --debbug 打印调试输出
 * -q, --quiet 安静模式，没有输出
 * -i, --input-file=FILE 下载在FILE文件中的urls
 

###下载参数

* -t, --tries=NUMBER 设定最大尝试链接次数（0表示无限制）
* -O, --output-document=FILE 把文档写入FILE中
* -c, --continue 接着下载没下载完的文件
* -N, –-timestamping 不要重新下载文件除非比本地文件新
* -r, --recursive 递归下载

##例子

1. 使用wget下载单个文件 `wget url`。
2. 使用wget -O 下载并命名 `wget -O filename url`。
3. 使用wget -c断点续传 `wget -c url`。
4. 使用wget -i下载多个文件
    
    在一个文件file中保存多条的urls（一行一条），执行`wget -i file`

5. 使用wget -o把下载信息存入日志文件 `wget -o download.log url`。

PS:这些都是最最基本的下载参数使用，更具体的可以参考`man wget`。
