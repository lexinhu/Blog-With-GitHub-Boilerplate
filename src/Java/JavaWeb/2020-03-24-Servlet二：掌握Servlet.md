---
layout: post
cid: 409
title: Servlet二：掌握Servlet
slug: 409
date: 2020/03/24 23:15:00
updated: 2020/03/24 23:43:22
status: publish
author: 心有所向
categories: 
  - Java
  - JavaWeb
tags: 
  - Servlet
---


## Servlet的生命周期

1、当浏览器第一次访问 Servlet 的时候，Tomcat 会查询当前 Servlet 的实例化对象是否存在，如果不存在，则通过反射机制动态创建对象，后执行第2步。如果已经存在，直接执⾏第 3 步。

2、调⽤ init ⽅法完成初始化操作。

3、调⽤ service ⽅法完成业务逻辑操作。

4、关闭 Tomcat 时，会调⽤ destory ⽅法，释放当前对象所占⽤的资源。

没有实际演示的介绍就是耍流氓，空洞无力。

### 演示：

我们创建一个ServletTest类，同样去实现Servlet接口，重写方法。并且我们映射到地址为`/test`

给上面介绍三个方法分别加入打印一句话的功能。

完整的代码如下：

```java
@WebServlet("/test")
public class ServletTest implements Servlet {
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        System.out.println("我是init");
    }

    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("我是service");
    }

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {
        System.out.println("我是destory");
    }
}
```

我们访问`http://localhost:8080/new_javaweb_test_war_exploded/test`，为了效果明显，我们多刷新几次该页面，回到idea控制台。可以看到，只有第一次会打印init，我们再配合上面的总结介绍，现在是不是就理解了。那你就应该知道如果我们现在停止运行，就会打印`我是destory`

![](https://cdn.xn2001.com/2020/03/24/20200324134305.png)



---



### 对象：

Java万物皆对象，可是我们演示中的ServletTest并没有在任何地方new出来呀。其实这个操作是Tomcat帮我们完成的，它通过反射机制调用无参构造方法创建了这个对象。这也很好的解释了为什么我们在xml配置映射时需要类的全路径，反射就是如此。

我们可以去写一个无参构造，看看情况。

```java
    public ServletTest() {
        System.out.println("我是构造方法，你的对象创建出来啦。");
    }

```

我们重新启动项目，重新在浏览器中访问，关闭运行。

在控制台查看信息。相信你就会理解了。

![](https://cdn.xn2001.com/2020/03/24/20200324135625.png)

---



### 方法：

⽆参构造函数、init、service、destory

1、⽆参构造函数只调⽤⼀次，创建对象。

2、init 只调⽤⼀次，初始化对象。

3、service 调⽤ N 次，执⾏业务⽅法。

4、destory 只调⽤⼀次，卸载对象。

## ServletConfifig

该接⼝是⽤来描述 Servlet 的基本信息的。

在init中，我们发现有这个接口的一个对象servletConfig，我们可以来研究看看它有什么方法。

![](https://cdn.xn2001.com/2020/03/24/20200324152018.png)



可以发现前面四个白色比较深的是这个接口的方法，后面几个是Object的。

根据方法名，我们先去测试getServletName，

我们直接将它打印出来即可

```java
System.out.println(servletConfig.getServletName());
```

可以看到它返回了 Servlet 的名称，全类名(带着包名的类名)

![](https://cdn.xn2001.com/2020/03/24/20200324152419.png)



getInitParameter(String key) 获取 init 参数的值（需要在web.xml中设置，在这里就不演示了，没什么用。）

getInitParameterNames() 返回所有的 `initParamter` 的 name 值，⼀般用作遍历初始化参数。

getServletContext() 返回 `ServletContext` 对象，它是 Servlet 的上下⽂，整个 Servlet 的管理者。

### ServletConfifig 和 ServletContext 的区别：

ServletConfifig 作用于某个 Servlet 实例，每个 Servlet 都有对应的 ServletConfifig，ServletContext 作用于整个 Web 应⽤，⼀个 Web 应⽤对应⼀个 ServletContext，多个 Servlet 实例对应⼀个ServletContext。⼀个是局部对象，⼀个是全局对象。

我们可以去用代码试试

```java
 System.out.println(servletConfig.getServletContext().getContextPath());
```

获取到了整个项目的路径，一个小小的点也能证明它是全局对象。

![](https://cdn.xn2001.com/2020/03/24/20200324171358.png)