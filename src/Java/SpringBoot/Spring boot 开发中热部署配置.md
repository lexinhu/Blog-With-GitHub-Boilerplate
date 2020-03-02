---
layout: post
title: Springboot 开发中热部署配置
slug: devtools-SpringBoot
date: 2020-03-01 15:34
status: publish
author: 乐心湖
categories: 
  - Java
  - SpringBoot
tags: 
  - SpringBoot
  - 技术笔记
---



## 第一步：添加 devtools 依赖

```
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
```

#### **要确保依赖成功导入**

![](https://cdn.xn2001.com/2020/02/29/20200229214431.png)

## 第二步，打开设置（ctrl+alt+s）

#### 搜索 Compiler 勾选上 Build project automatically

![](https://cdn.xn2001.com/2020/02/29/20200229214050.png)