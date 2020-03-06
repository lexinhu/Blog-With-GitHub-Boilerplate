```
layout: post
title: 思-关于maven的dependencyManagement
slug: dependencyManagement-maven
status: publish
author: 乐心湖
categories: 
  - Java
  - JavaWeb
tags: 
  - JavaWeb
  - maven
  - 技术笔记
```

## Dependency Management

我们不妨先去看看官方的文档。

> Besides inheriting certain top-level elements, parents have elements to configure values for child `POMs` and transitive dependencies. One of those elements is `dependencyManagement`.
>
> **dependencyManagement**: is used by a POM to help manage dependency information across all of its children. If the `my-parent` project uses `dependencyManagement` to define a dependency on `junit:junit:4.12`, then POMs inheriting from this one can set their dependency giving the `groupId`=`junit` and `artifactId`=`junit` only and Maven will fill in the `version` set by the parent. The benefits of this method are obvious. Dependency details can be set in one central location, which propagates to all inheriting POMs.
> Note that the version and scope of artifacts which are incorporated from transitive dependencies are also controlled by version specifications in a dependency management section. This can lead to unexpected consequences. Consider a case in which your project uses two dependences, `dep1` and `dep2`. `dep2` in turn also uses `dep1`, and requires a particular minimum version to function. If you then use `dependencyManagement` to specify an older version, `dep2` will be forced to use the older version, and fail. So, you must be careful to check the entire dependency tree to avoid this problem; `mvn dependency:tree` is helpful.

我简单用大白话说一下他在讲什么，这个东西是用来管理依赖统一的，当你在父级依赖中拥有一个依赖，他就可以替你在子模块中搞定他的版本，只要你的子模块`groupId`和`artifactId`跟父级模块名字一样即可。很明显的好处就是版本规范统一控制。之后他说了可能发生的错误，叫你小心管理好依赖树，这方面我们也不用担心太多，因为我们有idea。大家看看官方原话，看英语文档确实很难，好在程序员写技术文档都会用通俗易懂的词汇（这里我说的也包括中文文档）。

我们了解了这个东西之后，我寻思着为何没有例子，我把整个官方文档看了一遍也没找到，显然这不是Maven必需的，这样的版本规范控制，如果不是超大型项目又怎会用得到，大部分项目不过单模块业务处理，哪怕你是个多模块项目，也不外乎是三层提取，我们可以直接在父模块依赖中引入共有的，在子模块中引入自需的，我做项目便会如此。那么我为什么要写这个东西呢，因为我想将所有的学习源码都托管于Github，而不仅限于项目。这就导致我需要大量模块来进行分布，Maven就解决了我这一难题。

回到上面，官方没有给例子，我们就自己来，毕竟编程容错率是非常高的。

首先我们新建一个Maven的空项目，由于我们要用到多模块，我们将自带的src删除。

![](https://cdn.xn2001.com/2020/03/06/20200306063055.png)

我们来到项目下)的pom.xml，既然用到`dependencyManagement`，我们就直接上代码。这里直接使用mysql和Lombok这两个依赖作为演示。（考虑到使用一个依赖可能不具备说服力，三个又搞得像在科研，因此二个为最佳）

```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>8.0.18</version>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <version>1.18.10</version>
                <scope>provided</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

上面的写法使得版本号也在其中，依赖较多时不方便管理，我们养成习惯提取出来。

```xml
    <properties>
        <mysql.connector.version>8.0.18</mysql.connector.version>
        <lombok.version>1.18.10</lombok.version>
    </properties>
```

将原本的

`<version>8.0.18</version>`修改为`<version>${mysql.connector.version}</version>`

`<version>1.18.10</version>`修改为 `<version>${lombok.version}</version>`

我们更新一下Maven，会发现项目中并没有引入依赖，这就很好的说明了`dependencyManagement`和`dependencies`的区别，也展示出它的优越之处，子模块只需要放心使用依赖而无需考虑版本冲突问题。同时很好的解决了新手打包项目时优化不佳导致体积过大。

![](https://cdn.xn2001.com/2020/03/06/20200306065552.png)

接下来我们去新建一个Module(模块)，进入模块下的pom.xml

![](https://cdn.xn2001.com/2020/03/06/20200306071252.png)

我们加入在父模块中的两个依赖，看看是否能在子模块中引入。按照官方文档，你只要`groupId`和`artifactId`跟父模块一一对应，也就是名字相同即可。

```xml
    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
    </dependencies>
```

![](https://cdn.xn2001.com/2020/03/06/20200306072453.png)

结果跟预期的一样，我们再去新建一个模块，试着只引入mysql。

```xml
    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
    </dependencies>
```

![](https://cdn.xn2001.com/2020/03/06/20200306072750.png)

到这里我们的测试就已经完成了，我们不难发现版本管理这思想与SpringBoot有异曲同工之妙，但却比SpringBoot要灵活得多。



> 想来自己也是很用心写下这篇博客，与其说它是教程，不如说是对一个东西的思考与实践。所以我认为这篇博客不存在长篇大论，这也是我更愿意看到的技术博客。它面对的是新学习的人，如果你已经了解过这个东西，单单是因为忘记了一个知识点想通过百度来获取使用方法，那我更提倡你学习时为自己记录笔记，一方面提升你的知识索引速度，也增强你的知识掌握。所以我会将这篇博客再分离出一篇较简短的使用方法，来方便自己日后查阅。