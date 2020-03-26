---
layout: post
cid: 406
title: Tomcat在idea中乱码
slug: 406
date: 2020/03/24 19:14:26
updated: 2020/03/24 19:14:26
status: publish
author: 心有所向
categories: 
  - Java
  - JavaWeb
tags: 
  - 乱码
---


我们在idea中刚开始运行Tomcat时，会发现日志打印出来的是乱码的，这个问题理论上不需要去理会，我们一般都不去看，但是也有人会受不了，那么我们就去修改一下这个日志输出的编码即可

网络上的绝大部分教程都说改什么IDEA改什么虚拟机编码，这都是非常令人迷惑无语的行为。

我们去Tomcat目录下的conf中，修改logging.properties文件，在最底部加入下面的代码即可。

```properties
# 日志编码
java.util.logging.ConsoleHandler.encoding = GBK
```

![](https://cdn.xn2001.com/2020/03/24/20200324191215.png)