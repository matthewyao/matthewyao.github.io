---
layout:     post
title:      Java线程同步的六种方式
discription: 描述Java线程模型，什么是线程同步以及Java中线程同步的几种常用方式。
date:       2016-12-09 18:04:00
catalog:    true
tags:       [Java, 多线程, volatile, synchronized, 线程同步, ]
---

# Java线程同步的六种方式

> 理解线程同步之前，我们需要知道为什么Java中需要线程同步。首先我们需要清楚Java中的线程内存模型，JMM（Java Memory Model）规定了JVM有主内存(Main Memory)和工作内存(WorkingMemory)之分，主内存存放程序中所有的类实例、静态数据等变量，是多个线程共享的，而工作内存存放的是该线程从主内存中拷贝过来的变量以及访问方法所取得的局部变量，是每个线程私有的其他线程不能访问。

![JMM](http://oc26wuqdw.bkt.clouddn.com/blog/threadsync/jmm.jpgjmm.jpg)

> 所以在Java内存模型的规范下，单线程环境中，由于数据始终只有一个线程访问，所以不存在数据不同步的情况，但是在多线程的环境中，由于多线程工作内存数据会存在不同步的情况，会造成很多并发问题。

首先我们举一个例子说明下多线程环境下没有使用线程同步会有什么影响

```
public class TestMultiThread {

    public static int race = 0;

    public static void increse(){
        race++;
    }

    private static final int THREAD_COUNT = 100;

    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder();
        sb.append("");
        long start = System.currentTimeMillis();
        Thread[] threads = new Thread[THREAD_COUNT];
        for (int i=0; i<THREAD_COUNT; i++){
            threads[i] = new Thread(new Runnable() {
                @Override
                public void run() {
                    for (int i=0; i<10000; i++){
                        increse();
                    }
                }
            });
            threads[i].start();
        }

        //每次当主线程执行到这里的时候，主线程让出CPU资源，让所有线程（包括主线程）竞争CPU资源
        while (Thread.activeCount() > 2){
            Thread.yield();
        }

        //正常情况下每个线程都加了10000次，结果理应为300000，但由于自增非原子性，结果总会比300000小
        System.out.println(race);
        System.out.println("cost: " + (System.currentTimeMillis() - start ) + " mills");
    }
}

```

```
99545
cost: 16 mills
```

可以看到由于有线程在工作内存中的值没有同步更新，所以实际结果会小于预期的结果，所以这个时候就需要线程同步了。总结下来，Java中线程同步的方法主要有以下六种：

## 同步方法
同步方法是指使用Sychronized关键字修饰Java方法，使用Sychronized修饰的方法在线程进入执行前需要获取当前对象锁，若获取到则可继续执行，否则需要等待锁释放后方可进入

```
public synchronized static void increse(){
    race++;
}
```

```
1000000
cost: 20 mills
```

## 同步代码块
同步代码块类似于同步方法，只是Synchronized关键字加到代码上，同步是一种开销很高的操作，应该尽量缩小锁定的粒度，在实际应用中更推荐使用同步代码块

```
public static void increse(){
    synchronized (TestVolatile.class){
        race++;
    }
}
```

```
1000000
cost: 20 mills
```

## 可重入锁
JDK1.5之后在java.util.concurrent包来支持同步，其中的ReentrantLock类是可重入、互斥、实现了Lock接口的锁。它与使用同步方法和同步代码块具有相同的语义

```
private static Lock lock = new ReentrantLock();

public static void increse(){
    lock.lock();
    try{
        race++;
    }finally {
        lock.unlock();
    }
}
```

```
1000000
cost: 52 mills
```

## Volatile修饰变量
Volatile为变量的访问提供一种免锁机制，使用volatile修饰的变量在访问前需要强制重主存中刷新到工作内存，所以可以确保线程在操作变量时都是最新的副本。另外Volatile还可以禁止指令重排序,也即是：确保本条指令不会因编译器的优化而省略，且要求每次直接读值。

```
public static volatile int race = 0;

public static void increse(){
    race++;
}
```
```
1000000
cost: 52 mills
```

> 由于省略了锁机制，所以在可以使用Volatile的场景下还是尽量推荐使用，性能更好，代码更简洁，前提是：You always know what you are doing!

## 原子类
同时在JDK1.5 concurrent包中引入的还有原子类，使用AtomicInteger类可以确保自增的原子操作，原子类通过JNI的方式使用硬件支持的CAS指令，在性能上更好一些

```
private static AtomicInteger race = new AtomicInteger();

public static void increse(){
    race.incrementAndGet();
}
```
```
1000000
cost: 73 mills
```

## 线程变量
如果使用ThreadLocal管理变量，则每一个使用该变量的线程都获得该变量的副本，副本之间相互独立，这样每一个线程都可以随意修改自己的变量副本，而不会对其他线程产生影响。
