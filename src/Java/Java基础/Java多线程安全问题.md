---
layout: post
title: Java多线程的安全问题
slug: thread-safe-java
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

# 线程的安全问题

## 案例

**需求** ：某电影院目前正在上映国产大片，共有100张票，而他有3个窗口卖票，请设计一个程序模拟该电影院卖票

**思路** ：

1. 定义一个类Ticket实现Runnable接口，里面定义一个成员变量：
   ```private int ticketCount = 100;```

2. 在Ticket类中重写run()方法实现卖票，步骤如下：

   - 判断票数如果大于0，就卖票，并告知哪个窗口购买
   - 票数自动减一
   - 卖光之后，线程停止

3. 定义一个测试类TicketDemo，步骤如下：

   - 创建Ticket类的对象
   - 创建三个Thread类的对象，把Ticket对象作为构造方法的参数，并给出对应的窗口名称
   - 启动线程

## 代码实现

```java
public class Ticket implements Runnable {

    private int ticketCount = 100;

    @Override
    public void run() {
        while(true){
            if (ticketCount<=0){
                break;
            }else{
                this.ticketCount -= 1;
                System.out.println(Thread.currentThread().getName()+"正在卖票,还剩有"+this.ticketCount+"张");
            }
        }
    }
}
```

```java
public class Demo {
    public static void main(String[] args) {
        Ticket ticket = new Ticket();
        Thread t1 = new Thread(ticket,"一号窗口");
        Thread t2 = new Thread(ticket,"二号窗口");
        Thread t3 = new Thread(ticket,"三号窗口");
        t1.start();
        t2.start();
        t3.start();
    }
}
```

## 卖票案例的思考

看似这个案例没有什么问题,但是在实际生活中,售票时候出票是需要一定的时间的,所以在出售一张票的时候需要一点时间的延迟,接下来就修改卖票程序中的动作,每次出票时间为100毫秒,用sleep()方法实现。

此时出现了问题

   - 相同的票出现了多次
   - 出现的负数的票

为什么出现这个问题（这也是我们判断多线程程序是否会有数据安全问题的标准）

   - 多线程操作共享数据

如果解决多线程安全问题？

   - 基本思想：让程序没有安全问题的环境

实现方法

   - 把多条语句操作共享数据的代码**锁**起来，让任意时刻只能有一个先吃执行。

## 同步代码块

锁多条语句操作共享数据，可以使用同步代码块实现

- 格式：

  synchronized(任意对象){ 多条语句操作共享数据的代码 }
  
- 默认情况是打开的，只要有一个线程进去执行代码了，锁就会关闭。

- 当线程执行完了之后，锁就会自动打开。

同步的好处和弊端

- 好处：解决了多线程的数据安全问题
- 弊端：当线程很多时，因为每个线程都会去**判断**同步上的锁，这是非常浪费资源的，无形中降低了程序的运行效率

下面我们更新一下Ticket类。

```java
public class Ticket implements Runnable {

    private int ticketCount = 100;
    private Object o = new Object();

    @Override
    public void run() {
        while(true){
        
            //锁对象,任意对象就可以.
            //多个线程必须使用同一把锁
            synchronized (o){
                if (ticketCount==0){
                    break;
                }else{
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    this.ticketCount -= 1;
                    System.out.println(Thread.currentThread().getName()+"正在卖票,还剩有"+this.ticketCount+"张");
                }
            }
        }
    }
}
```

![](https://cdn.xn2001.com/2020/02/05/20200205011342.png)

此时的运行结果变得非常自然。

### synchronized的锁对象必须是唯一的

## 同步方法

同步方法：就是把`synchronized`关键字加到方法上。

- 格式：

  修饰符 synchronized 返回值类型 方法名(方法参数){ }

同步静态方法：就是把synchronized关键词加到静态方法上

- 格式：

  修饰符 static synchronized 返回值类型 方法名(方法参数){ }

主要记住：

1. 同步方法到底锁对象是什么？ **this**

2. 同步静态方法的锁对象是什么？ **类名.class**

## 同步代码块和同步方法的区别

- 同步代码块可以锁住指定代码，同步方法是锁住方法中所有代码。
- 同步代码块可以指定锁对象，同步方法不能**指定**锁对象。

## ★Lock锁

虽然我们可以理解同步代码块和同步方法的锁对象，但是我们并没有直接看到在哪里加上了锁，在哪里释放了锁，为了更加清晰的表达如何加锁和释放锁，JDK5以后提供了一个新的锁对象Lock

Lock实现提供比使用synchronized方法和语句可以获得更广泛的锁定操作

Lock中提供了获得锁和释放锁的方法

- void lock()             获得锁
- void unlock()        释放锁

Lock是接口不能直接实例化，可以采用它的实现类`ReentrantLock`来实例化

ReentrantLock的构造方法

- ReentrantLock()  创建一个ReentrantLock的实例

#### **注意**：一般释放锁unlock要放入**finally**中。

下面我们使用Lock来更新一下Ticket类

```java
public class Ticket implements Runnable {

    private int ticketCount = 100;
    
    //锁对象Lock
    private Lock lock = new ReentrantLock();

    @Override
    public void run() {
        while (true) {
            //开启Lock锁
            lock.lock();
            try {
                if (ticketCount == 0) {
                    break;
                } else {
                    Thread.sleep(100);
                    this.ticketCount -= 1;
                    System.out.println(Thread.currentThread().getName() + "正在卖票,还剩有" + this.ticketCount + "张");
                }
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                //释放Lock锁
                lock.unlock();
            }
        }
    }
}
```



# 死锁

线程死锁是指由于两个或者多个线程互相持有对方所需要的资源，导致这些线程处于等待状态，无法前往执行。

建议：不要写锁的嵌套。