---
layout: post
title: SpringBoot配置阿里Druid连接池
slug: druid-connect-springboot
date: 2020-03-16 15:34
status: publish
author: 乐心湖
categories: 
  - Java
  - SpringBoot
tags: 
  - SpringBoot
  - 技术笔记
  - druid
  - 连接池
---

为什么使用阿里的Druid，主要的一点就是功能多，而且喜欢阿里！

具体场景使用hikari还是druid根据需求，两个都应该有所了解。

## 如何在 SpringBoot 配置 druid 连接池



### 一.  首先确保有相关依赖，检查pom.xml 

```xml
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.21</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
```

### 二. 在application.yml中配置相关属性

> 这里需要提一下的是，当初我在学习这个的时候，教学说到druid需要注入bean再通过配置类来引入配置，但我自己测试中发现实际上无需这么麻烦。

里面的配置可以自行就行增删改。

```yml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/mybatis?characterEncoding=UTF-8&useSSL=true&serverTimezone=UTC
    username: root
    password: 123456
    platform: mysql
    type: com.alibaba.druid.pool.DruidDataSource
    druid:
      # 初始化大小，最小，最大
      initialSize: 1
      minIdle: 3
      maxActive: 20
      # 配置获取连接等待超时的时间
      maxWait: 60000
      # 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
      timeBetweenEvictionRunsMillis: 60000
      # 配置一个连接在池中最小生存的时间，单位是毫秒
      minEvictableIdleTimeMillis: 30000
      validationQuery: select 'x'
      testWhileIdle: true
      testOnBorrow: false
      testOnReturn: false
      # 是否缓存preparedStatement，打开PSCache，并且指定每个连接上PSCache的大小。官方建议MySQL下建议关闭，如果用SQL防火墙 建议打开
      poolPreparedStatements: true
      maxPoolPreparedStatementPerConnectionSize: 20
      # 配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙
      filters: stat,wall,slf4j
      # 通过connectProperties属性来打开mergeSql功能；慢SQL记录
      connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000
```





<img src="https://cdn.xn2001.com/2020/03/16/20200316123113.png" style="zoom:90%;" />

### 三. 进行测试

我们在test中直接进行简单的debug即可。

我们对打印这里进行断点，点击一下行数显示17的那个位置旁边即可。记得是右键使用debug而不是run。

```java
@SpringBootTest
class DemoApplicationTests {

    @Autowired
    DataSource dataSource;

    @Test
    void contextLoads() {
        System.out.println(dataSource.getClass());
    }

}
```

![](https://cdn.xn2001.com/2020/03/16/20200316124432.png)

![](https://cdn.xn2001.com/2020/03/16/20200316124815.png)

我们可以清晰的看到我们的配置成功引入，并且跟yml配置文件一模一样。

这里我们的连接池就算配置完成了。