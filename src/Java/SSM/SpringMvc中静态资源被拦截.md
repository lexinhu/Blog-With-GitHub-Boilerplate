---
layout: post
cid: 365
title: SpringMvc中静态资源被拦截
slug: mvc-Default
date: 2020/02/13 21:02:00
updated: 2020/02/26 13:51:11
status: publish
author: 心有所向
categories: 
  - Java
  - SSM
tags: 
  - SpringMvc
  - 技术笔记
---


## 第一种：在web.xml中配置名为default的servlet-mapping
    ```xml
    <servlet-mapping>
        <servlet-name>default</servlet-name>
        <url-pattern>*.css</url-pattern>
    </servlet-mapping>
    <servlet-mapping>
        <servlet-name>default</servlet-name>
        <url-pattern>*.gif</url-pattern>
    </servlet-mapping>
    <servlet-mapping>
        <servlet-name>default</servlet-name>
        <url-pattern>*.png</url-pattern>
    </servlet-mapping>
    <servlet-mapping>
        <servlet-name>default</servlet-name>
        <url-pattern>*.js</url-pattern>
    </servlet-mapping>
    ```


## 第二种：在SpringMVC配置文件中添加<mvc:resource>标签

```xml
<mvc:resources mapping="/js/**" location="/js/"/>
<mvc:resources mapping="/css/**" location="/css/"/>
<mvc:resources mapping="/img/**" location="/img/"/>
```


## 第三种(推荐)：在SpringMVC配置文件中添加<mvc:default-servlet-handler/>标签

```xml
<mvc:default-servlet-handler/>
```


## 第四种：修改SpringMVC的拦截路径，我这里使用的是/，也就是说所有的链接的拦截，

可以修改成*.do或者*.action或者其他，这样静态资源就不会拦截了
```xml
<servlet-mapping>
<servlet-name>spring-mvc</servlet-name>
<url-pattern>/</url-pattern>
</servlet-mapping>
```