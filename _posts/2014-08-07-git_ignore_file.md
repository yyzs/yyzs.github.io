---
layout: post
title: "git忽略文件gitignore"
description: ""
category: 开发工具
tags: [git,github]
---

在把远程的版本库clone到本地时发现，在mac osx下，文件夹里多了一个.DS_Store。

>.DS_Store是Mac OS保存文件夹的自定义属性的隐藏文件，如文件的图标位置或背景色，相当于Windows的desktop.ini。

我可不希望这个文件加入到版本库里面，所以我在项目的根目录加了一个文件, `.gitignore`。

在git中如果想忽略某些文件，不让这些文件提交到版本库中，可以使用 .gitignore文件的方法。

凡是在匹配.gitignore文件中的文件，都会被git忽略。这个文件每一行保存了一个匹配规则，例如：

    # 这是注释
    *.pyc # 忽略所有以.pyc结尾的文件
    !lib.pyc # 但 lib.pyc 除外
    /TODO # 忽略项目根目录下的TODO文件
    build/ # 忽略build/目录下地所有文件
    doc/*.txt # 忽略doc/目录下以txt为后缀的文件，但不包括doc子目录下的txt文件

PS: 不知道为什么最近的wordpress访问实在是太慢了，最终我又把博客迁回了github上，不打算再换了。
