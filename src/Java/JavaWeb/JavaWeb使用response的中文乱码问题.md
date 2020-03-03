---
layout: post
title: JavaWeb使用response的中文乱码问题
slug: encoding-javaweb
date: 2020-03-03 15:34
status: publish
author: 乐心湖
categories: 
  - Java
  - JavaWeb
tags: 
  - JavaWeb
  - 踩坑
  - 技术笔记
---



原生JavaWeb使用response的中文乱码问题

继承HttpServlet后重写方法时加入两行代码设置utf-8编码

```java
resp.setContentType("application/json;charset=utf-8");
resp.setCharacterEncoding("utf-8");
//需要加上方法的最上面,也就是在方法内容的首位.
```

完整示例：

```java
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("application/json;charset=utf-8");
        resp.setCharacterEncoding("utf-8");
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        out.println("<HTML>");
        out.println("<HEAD><TITLE>Hello World</TITLE></HEAD>");
        out.println("<BODY>");
        out.println("关键就是最上面两行代码,用于设置编号");
        out.println("</BODY></HTML>");
    }
```

