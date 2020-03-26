---
layout: post
cid: 410
title: Servlet三：真正开发中的Servlet
slug: 410
date: 2020/03/24 23:17:00
updated: 2020/03/24 23:43:16
status: publish
author: 心有所向
categories: 
  - JavaWeb
tags: 
  - Servlet
---


在实际的开发中，我们几乎不需要使用到init和destory，我们只用到service，所以官方帮我们最后优化出了HttpServlet，我们开发项目时直接去继承即可。

## Servlet的层次结构

**Servlet ---> GenericServlet ---> HttpServlet**

GenericServlet 实现 Servlet 接⼝，同时为它的⼦类屏蔽了不常用的方法，⼦类只需要重写 service ⽅法即可。

HttpServlet 继承 GenericServlet，根据请求类型进⾏分发处理，GET 进⼊ doGET ⽅法，POST 进⼊doPOST 方法。

开发者⾃定义的 Servlet 类只需要继承 HttpServlet 即可，重新 doGET 和 doPOST。

## HTTP 请求

HTTP 请求有很多种类型，常⽤的有四种：

- GET 读取

- POST 保存

- PUT 修改

- DELETE 删除

## 开发使用

完整代码：

```java
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * Created by 乐心湖 on 2020/3/24 17:31
 */
@WebServlet("/http")
public class HttpServletTest extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("Hello World");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}
```

![](https://cdn.xn2001.com/2020/03/24/20200324173554.png)