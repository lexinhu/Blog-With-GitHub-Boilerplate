---
layout: post
title: Java多线程的常见方法
slug: thread-void-java
date: 2020-03-09 13:10
status: publish
author: 乐心湖
categories: 
  - Java
  - Java基础
tags: 
  - Java基础
  - 多线程
  - 学习笔记
---

## 三种多线程实现方式的优缺点 

|                            | 优点                                         | 缺点                                    |
| -------------------------- | -------------------------------------------- | --------------------------------------- |
| 实现Runnable、Callable接口 | 扩展性强，实现该接口的同时还可以继承其他的类 | 编程相对复杂,不能直接使用Thread类的方法 |
| 继承Thread类               | 编程比较简单，可以直接使用Thread类中的方法   | 扩展性较差，不能继承其他的类            |

## 获取和设置线程名称

获取线程的名称

- String getName() ：返回此线程的名称

设置线程的名称

- 直接在创建对象时通过构造方法传参
- void setName(String name) 

```java
public class MyThread extends Thread {
    public MyThread() {}
    
    public MyThread(String name) {
        super(name);
    }
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println(getName()+"线程开启了" + i);
        }
    }
}
```

```java
public class MyThreadTest {
    public static void main(String[] args) {
        MyThread t1 = new MyThread("小明");
        MyThread t2 = new MyThread("小红");

//        使用setName()
//        t1.setName("小明");
//        t2.setName("小红");
        
        t1.start();
        t2.start();
    }
}
```
注意：线程是有默认名字的，为Thread-编号

## 获取当前线程的对象

- public static Thread currentThread() ：返回当前正在执行的线程对象的引用
- 作用：当使用 实现Runnable 创建线程时，不可以直接使用getName()，此时应该用 Thread.currentThread().getName()

```java
public class MyRunnable implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println(Thread.currentThread().getName()+"第二种方式"+i);
        }
    }
}
```

## 线程休眠

- public static void sleep(long time) ：让线程休眠指定的时间，单位为毫秒。 

```java
    @Override
    public void run() {
        while (true) {
            try {
                //线程休眠
                Thread.sleep(800);
                System.out.println(" ----First Thread----");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
```



## 后台线程/守护线程

- 当线程停止时，守护线程也随之停止。
- public final void sedDaemon(boolean on) ：设置为守护线程

```java
public class test {
    public static void main(String[] args) {
        Runnable1 runnable1 = new Runnable1();
        Runnable2 runnable2 = new Runnable2();

        Thread thread1 = new Thread(runnable1,"线程一");
        Thread thread2 = new Thread(runnable2,"线程二");

//        设置线程二为守护线程
        thread2.setDaemon(true);
        thread1.start();
        thread2.start();
    }
}

class Runnable1 implements Runnable{

    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println(Thread.currentThread().getName()+"执行了"+i);
        }
    }
}

class Runnable2 implements Runnable{

    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println(Thread.currentThread().getName()+"执行了"+i);
        }
    }
}
```

## 线程调度多线程的并发运行：

计算机中的CPU，在任意时刻只能执行一条机器指令。每个线程只有获得CPU的使用权才能执行代码。各个线程轮流获得CPU的使用校，分别执行各自的任务。

## 线程调度

线程有两种调度模型·分时调度模型：

- 所有线程轮流使用CPU的使用权，平均分配每个线程占用CPU的时间片
- 抢占式调度模型：优先让优先级高的线程使用CPU，如果线程的优先级相同，那么会雕机选择一个，优先级高的线程获取的CPU时间片相对多一些
- Java使用的是抢占式调度模型

## 线程的优先级

- public final void setPriority(int newPriority)    设置线程的优先级
- public  final int getPriority()   获取线程的优先级
- 线程的优先级1-10，默认为5
- 优先级更高，抢夺到cpu执行权的机率更高，但仅仅只是机率。

```java
public class RunnableTest {
    public static void main(String[] args) {
        MyRunnable1 runnable1 = new MyRunnable1();
        MyRunnable2 runnable2 = new MyRunnable2();
        Thread thread1 = new Thread(runnable1);
        Thread thread2 = new Thread(runnable2);

        //设置线程优先级
        thread1.setPriority(8);
        thread2.setPriority(1);

        //打印出线程一的优先级,不设置时默认为5
        System.out.println(thread1.getPriority());

        thread1.start();
        thread2.start();
    }
}

class MyRunnable1 implements Runnable {
    @Override
    public void run() {
        while (true) {
            try {
                //线程休眠
                Thread.sleep(800);
                System.out.println(" ----First Thread----");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

class MyRunnable2 implements Runnable {
    @Override
    public void run() {
        while (true) {
            try {
                //线程休眠
                Thread.sleep(1500);
                System.out.println(" ----Second Thread----");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## 线程生命周期

![](https://cdn.xn2001.com/2020/01/1809456008.png#vwid=1049&vhei=381)
