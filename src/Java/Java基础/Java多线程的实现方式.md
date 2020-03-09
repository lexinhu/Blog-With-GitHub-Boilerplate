---
layout: post
title: Java多线程的实现方式
slug: thread-know-java
date: 2020-03-09 12:10
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

## 并发和并行

并发:在同一时刻,有多个指令在**单个CPU**上**交替**执行.

并行:在同一时刻,有多个指令在**多个CPU**上**同时**执行.

## 进程和线程

进程:正在运行的软件.

- 独立性:进程是一个能独立运行的基本单位,同时也是系统分配资源和调度的独立单位.
- 动态性:进程的实质是程序的一次执行过程,进程是动态产生,动态消亡的.
- 并发性:任何进程都可以同其他进程一起并发执行.

线程:进程中的单个顺序控制流,是一条执行路径.

## 多线程的实现方式

- 继承Thread类的方式进行实现
- 实现Runnable接口的方式进行实现
- 利用Callable和Future接口方式实现



## 多线程的实现方案

### 方式1：继承Thread类

1. 定义一个类如MyThread继承Thread类
2. 在MyThread类中重写run()方法
3. 创建MyThread类的对象
4. 启动线程

```java
public class MyThread extends Thread {
    @Override
    public void run() {
//        run方法是执行的内容
        for (int i = 0; i < 100; i++) {
            System.out.println("线程开启了"+i);
        }
    }
}
```

```java
public class MyThreadTest {
    public static void main(String[] args) {
        MyThread t1 = new MyThread();
        MyThread t2 = new MyThread();
        t1.start();
        t2.start();
//        可以发现两个线程是交替执行的
    }
}
```

#### 两个小问题
为什么要重写run方法？
- 因为run是用来封装被线程执行的代码
- run()方法和start()方法的区别？
- run()：封装线程执行的代码，直接调用，相当于普通方法的调用，并没有开启线程.
- start()：启动线程；然后由VM调用此线程的run()方法.

### 方式2：实现Runnable

1. 定义一个类MyRunnable实现Runnable接口

2. 在MyRunnable类中重写run()方法

3. 创建MyRunnable类的对象

4. 创建Thread类的对象，把MyRunnable对象作为构造方法的参数

5. 启动线程

```java
public class MyRunnable implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println("第二种方式"+i);
        }
    }
}
```

```java
public class MyRunnableTest {
    public static void main(String[] args) {
//        创建一个参数对象
        MyRunnable m1 = new MyRunnable();
        MyRunnable m2 = new MyRunnable();
//        创建一个线程对象,并把参数传给这个线程
        Thread t1 = new Thread(m1);
        Thread t2 = new Thread(m2);
//        开启线程
        t1.start();
        t2.start();
    }
}
```

### 方式3：Callable和Future
1. 定义一个类MyCallable实现Callable接口
2. 在MyCallable类中重写call()方法·创建MyCallable类的对象
3. 创建Future的实现类FutureTask对象，把MyCallable对象作为构造方法的参数
4. 创建Thread类的对象，把FutureTask对象作为构造方法的参数
5. 启动线程
6. 再调用get方法，就可以获取线看程结束之后的结果。
7. 值得注意的是:get方法要在start开启之后调用.

```java
public class MyCallable implements Callable<String>{
    @Override
    public String call() throws Exception {
        for (int i = 0; i < 100; i++) {
            System.out.println("线程执行中"+i);
        }
        return "线程执行完成";
    }
}
```

```java
public class MyCallableTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        MyCallable m1 = new MyCallable();
//        可以获取线程执行后的结果,也可以作为参数传递诶Thread
        FutureTask<String> f1 = new FutureTask(m1);
        Thread t1 = new Thread(f1);
//        开启线程
        t1.start();
//        返回值
        String o = f1.get();
        System.out.println(o);
    }
}
```