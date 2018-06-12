---
layout: post
title: 解决pip安装python库速度慢的问题
comments: true
categories: Python
tags: pip python
---

### 背景

使用pip安装python一些库的时候，默认使用的是国外的源，安装非常慢，通常只有10几KB，而且动不动就time out。

### 解决方法

更换国内的源，更换方法，参考[官方文档](https://pip.pypa.io/en/latest/user_guide/#requirements-files)

新建一个环境变量*PIP_CONFIG_FILE*，然后指定配置文件位置，我的是*C:\Python27\pip.ini*

然后在C:\Python27下面新建pip.ini文件：

```
[global]
index-url = https://pypi.douban.com/simple
[install]
trusted-host=pypi.douban.com
```

然后你再安装一些库试试，速度飞快！
