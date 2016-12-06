---
layout: post
title: "每天一个linux命令(19)之chgrp"
description: "linux命令chgrp"
category: 每天一个linux命令
tags: [linux, command]
---

在linux中要改变一个文件的用户组很简单，`chgrp`命令就可以了。`chgrp`命令就是`change group`的缩写。用来改变文件的用户组。要改变的组名必须是在`/etc/group`文件中存在的。

一般只有管理员才有权限使用这个命令。

##格式

```sh
chgrp [参数] [组名] [文件]
```

##参数

* -R 递归地修改子目录下所有文件
* -v 运行时显示详细的处理信息

##例子

1. 修改一个文件的群组为user： `chgrp user filename`
