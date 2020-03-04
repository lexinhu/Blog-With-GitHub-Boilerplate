---
layout: post
title: 纯Java搭建SpringMVC项目
slug: mvc-Java
date: 2020/03/04 15:00:11
status: publish
author: 心中有湖
categories: 
  - Java
  - SSM
tags: 
  - SpringMVC
  - 技术笔记
---

# 纯Java搭建SpringMVC项目

## 1. 创建工程

这里我们创建一个普通Maven工程，并添加 SpringMVC 的依赖，这里环境的搭建需要用到 Servlet ，所以我们还需要引入 Servlet 的依赖，最终pom.xml 添加的内容如下：

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.2.2.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>4.0.1</version>
        <scope>provided</scope>
    </dependency>
</dependencies>
```

## 2. 添加SpringMVC配置类

```java
@Configuration
@ComponentScan("com.xn2001")
public class SpringMVCConfig {

}
```

@Configuration标注这个类为配置类

@ComponentScan("com.xn2001")，扫描这个包下里面的Bean。

这里如果不需要在 SpringMVC 中添加其他的额外配置，这样就可以了。

## 3. 添加web.xml配置类

```java
public class WebInit implements WebApplicationInitializer {

    public void onStartup(ServletContext servletContext) throws ServletException {
        AnnotationConfigWebApplicationContext applicationContext = new AnnotationConfigWebApplicationContext();
        applicationContext.register(SpringMVCConfig.class);
        ServletRegistration.Dynamic springmvc= servletContext.addServlet("springmvc",new DispatcherServlet(applicationContext));
        springmvc.addMapping("/");
        springmvc.setLoadOnStartup(1);
    }
}
```

### 对比使用web.xml

```xml
    <servlet>
        <servlet-name>SpringMVC</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>SpringMVC</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
```

对比我们不难看出，使用xml或者类的方式，其本质都是一样的，添加入一个SpringMVC中央处理器，设置配置文件和初始化启动优先级，最后进行mapping。关于里面更深层的东西，我并不觉得自己多懂，也不想在这里长篇大论。

这里为什么没有写Spring配置类，如果你简单开发过SSM整合的项目，你不难发现其实直接将所有配置放到 SpringMVC 的配置中来完成是没有问题的。



## 4. 添加HelloController

```java
@RestController
public class HelloController {
    @GetMapping("/hello")
    public String hello() {
        return "Hello,World";
    }
}
```

@RestController是@ResponseBody+@Controller的组合，

声明为控制类

且类下的所有方法的返回值(return)由Java对象转为json格式输出，这样我们在浏览器直接看到了内容了。

## 5. 启动项目

我们需要前往pom.xml中加入下面一行代码，设置打包方式。之后放入Tomcat容器启动项目即可，关于如何在idea中加入Tomcat项目，这里推荐自己之前写过的一篇非常[详细的演示](https://www.xn2001.com/archives/386.html)。

```
<packaging>war</packaging>
```

如果你启动时首页是404说明已经成功了，这时候在网址后加入hello就看到我们输出的 Hello，World 了。