---
layout: post
title: "爬取豆瓣妹子上的照片"
description: ""
category: python
tags: [python,BeautifulSoup,爬虫]
---

在网上看到有人用python写了一个爬取豆瓣妹子的照片，我也来重复造轮子，练练手。

主要是用到了urllib2这个模块来获取网页，取得html页面代码，然后用BeautifulSoup进行html的解析。

BeautifulSoup 不在python的标准库中，所以需要安装。

下载地址：http://www.crummy.com/software/BeautifulSoup/#Download

下载后解压，然后进入目录，执行

```
python setup.py build
python setup.py install
```

注意：这里`python setup.py install`需要以root身份安装。

程序源码如下：

```
#!/usr/bin/env python
# _*_ coding:utf-8 _*_

import os
import urllib2
from bs4 import BeautifulSoup

#baseurl = 'http://dbmeizi.com/category/1' #性感
#baseurl = 'http://dbmeizi.com/category/2' #有沟
#baseurl = 'http://dbmeizi.com/category/3' #美腿
#baseurl = 'http://dbmeizi.com/category/11' #小清新
baseurl = 'http://dbmeizi.com/category/12'  #文艺
#baseurl = 'http://dbmeizi.com/category/14'  #美臀
header = {'User-Agent':'Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US; rv:1.9.1.6) Gecko/20091201 Firefox/3.5.6'}
dirpath = os.getcwd()

def save_image(src):
    result = urllib2.urlopen(src)
    data = result.read()
    # 分割src，得到文件名
    path = src.split('/')
    filename = path.pop()
    filename = dirpath + '/images/' + filename
    # 保存
    f = open(filename, 'wb')
    f.write(data)
    f.close()

def one_page(url):
    try:
        req = urllib2.Request(url = url, headers = header)
        html = urllib2.urlopen(req)
    except urllib2.URLError as e:
        print e.message
    return html.read()

def page_loop(page_id):
    url = baseurl + "?p=%d" % page_id
    print "开始爬取 %s " % url
    page = one_page(url)
    soup = BeautifulSoup(page)
    images = soup.find_all('img')
    number = 0
    for img in images:
        src = img.get('src')
        number += 1
        print "%d. %s" %(number, src)
        save_image(src)
    return number

page_start = 0 #开始页数
page_stop = 5  #结束页数

total = 0
print "爬虫开始活动......"
for i in range(page_start, page_stop):
    total += page_loop(i)

print "爬虫结束活动......"
print "报告：总计有 %d 张" % total
```
