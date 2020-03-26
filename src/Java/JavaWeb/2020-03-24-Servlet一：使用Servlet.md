---
layout: post
cid: 408
title: Servlet一：使用Servlet
slug: 408
date: 2020/03/24 23:13:00
updated: 2020/03/24 23:16:57
status: publish
author: 心有所向
categories: 
  - Java
  - JavaWeb
tags: 
  - Servlet
---


### 什么是 Servlet？

Servlet 是 Java Web 开发的基⽯，与平台⽆关的服务器组件，它是运⾏在 Servlet 容器/Web 应⽤服务器/Tomcat，负责与客户端进⾏通信。
主要功能：

- 创建并返回基于客户请求的动态 HTML ⻚⾯。
- 与数据库进⾏通信。

---



### 如何使用Servlet？

Servlet 本身只是⼀组接⼝，我们需要定义⼀个类，并且实现 Servlet 接⼝，这个类就具备了接受客户端请求以及做出响应的功能。

我们到项目中去学习，新建一个MyServlet类，实现Servlet接口，重写方法

```java
public class MyServlet implements Servlet {
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        //初始化工作
    }

    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
		//主要的核心，操作基本都写在这里，主要的业务实现。
    }

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {
		//常用于释放资源
    }
}
```

我们在service方法中写一些代码，看看效果。

```java
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("我是servlet，我已经在工作了 ！");
    }
```

到这里我们会有一个疑问，我们如何访问这个servlet类呢，由于浏览器并不能直接访问 Servlet ⽂件，我们只能通过映射的⽅式来间接访问 Servlet，映射需要开发者⼿动配置，有**两种**配置⽅式。

---



### 两种方式配置映射

#### 基于XML文件

注意：是写在WEB-INF下的web.xml中

![](https://cdn.xn2001.com/2020/03/24/20200324121959.png)

#### 基于注解

直接在MyServlet类上面添加一个注解`WebServlet`括号中为访问地址

```java
@WebServlet("/hello")
public class MyServlet implements Servlet{}
```

---



只要其中一种就可以了，不用同时使用。

我们到浏览器中去访问`http://localhost:8080/new_javaweb_test_war_exploded/hello`

页面空白，但是没有报错，所以我们成功访问了，我们到控制台查看是否有打印出一句话。

很明显，我们成功访问到了自己写的Servlet。

![](https://cdn.xn2001.com/2020/03/24/20200324122803.png)

那么我们如何往页面中输出点东西呢，同样的，我说了功能代码都应该写在service方法中

```java
servletResponse.getWriter().write("这里是我的Servlet，欢迎进入新世界 ！");
```

我们加入代码后在浏览器中发现中文乱码了，这时候不用慌，我们在这句代码这前再加一句

```java
servletResponse.setContentType("text/html;charset=UTF-8");
```

此时该方法完整的代码为

```java
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("我是servlet，我已经在工作了 ！");
        servletResponse.setContentType("text/html;charset=UTF-8");
        servletResponse.getWriter().write("这里是我的Servlet，欢迎进入新世界 ！");
    }
```

运行就可以看到页面输出结果啦。

![](https://cdn.xn2001.com/2020/03/24/20200324125819.png)

### 使用Debug

我推荐大家使用Debug来运行项目，这样可以节省我们在启动项目的大量时间。

在idea中可以做到直接重新编译你修改的东西，而不用整个项目重新启动。

如果做到这一点，首先你要确保你开启了update classes and resources，运行项目时使用Debug。

![](https://cdn.xn2001.com/2020/03/24/20200324130124.png)

![](https://cdn.xn2001.com/2020/03/24/20200324130221.png)

![](https://cdn.xn2001.com/2020/03/24/20200324130258.png)

### servletRequest

细心的你不难发现，我们的service方法中还没有使用到servletRequest参数，其实这个是用来获取参数之类的。

示例：

```java
		String id = servletRequest.getParameter("id");
        servletResponse.getWriter().write("你好，我们已经获取到你的ID为："+id);
```

Debug运行，发现浏览器中显示ID为Null，此时你在地址后面加入`?id=10086`，就可以看到效果啦。

![](https://cdn.xn2001.com/2020/03/24/20200324130814.png)

![](https://cdn.xn2001.com/2020/03/24/20200324130915.png)

