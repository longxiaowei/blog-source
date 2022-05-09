---
layout: post
title:  "volatile 详解"
date:   2020-05-15
categories:
 - j.u.c
tags:
 - java
 - J.U.C
 - java并发
color: rgb(230,230,250)
cover: '../assets/blogimg/2018-10-24.png'
---
# volatile 简介
  volitile 是 java 种的一个关键字，volatile主要两个作用
- 保证线程可见性
- 禁止指令重排序

# 线程可见性
  线程可见性是指多个线程访问同一变量的时候，某一个线程修改了该变量的值，其他线程能够立即看到修改后的值。测试代码：

- 不使用 volatile 修饰

```
  public class VolatileDemo {

    private static boolean running = true;

    public static void main(String[] args) throws InterruptedException {
        new Thread(() -> {
            while(running){
            }
        }).start();

        Thread.sleep(4000);
        running = false;
    }
}
```
运行结果：

![](/images/volatile/no-volatile-demo-running-result.jpg)

- 使用 volatile 修饰

```
public class VolatileDemo {

    private static volatile boolean running = true;

    public static void main(String[] args) throws InterruptedException {
        new Thread(() -> {
            while(running){
            }
        }).start();

        Thread.sleep(4000);
        running = false;
    }
}
```
运行结果：

![](/images/volatile/volatile-demo-running-result.jpg)

不使用 volatile 修饰程序执行结果：线程未结束。说明 while 条件一直成立，即线程读取到的 running值为true。
使用 volatile 修饰，程序线程可以正常退出。说明while条件读到的running值为false。
结论：被validate修饰的变量，可以保持线程可见性。

# 指令重排序
CPU为了优化指令执行效率，会对需要执行的指令进行重排序。

