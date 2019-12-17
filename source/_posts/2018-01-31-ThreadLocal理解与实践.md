---
title: ThreadLocal理解与实践
date: 2018-01-31 16:26:03
categories: 
- [编程]
- [Java]
tags:
- Java
---



​		首先贴出源码的对于该类的注释，大意讲的是这个类提供了线程封闭的变量。所谓线程封闭指的是其他线程不可以访问这个变量，每一个线程对应该变量的一个副本。当ThreadLocal被声明成private static时，一般用来将一个属性与当前线程联系起来（比如当前的线程ID，用户ID）。

```java
/**
 * This class provides thread-local variables.  These variables differ from
 * their normal counterparts in that each thread that accesses one (via its
 * {@code get} or {@code set} method) has its own, independently initialized
 * copy of the variable.  {@code ThreadLocal} instances are typically private
 * static fields in classes that wish to associate state with a thread (e.g.,
 * a user ID or Transaction ID).
 */
```

### 使用

> 官方文档给了一个示例：

```java
public class d_ReorderingDemo {

    private static class ReaderThread extends Thread {

        private static final AtomicInteger threadId = new AtomicInteger(0);
        private static final ThreadLocal<Integer> threadLocal =
          ThreadLocal.withInitial(threadId::getAndIncrement);//这里只是λ表达式的初始化形式

        public long getId() {
            return threadLocal.get();
        }

        @Override
        public void run() {
            System.out.println("当前线程Id:" + threadLocal.get());
            System.out.println("当前AtomicInteger的值：" + threadId.get());
        }
    }

    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
            new ReaderThread().start();
        }
    }
}

//输出：=======================================================
当前线程Id:0
当前AtomicInteger的值：1
当前线程Id:1
当前AtomicInteger的值：2
当前线程Id:2
当前AtomicInteger的值：3
当前线程Id:3
当前AtomicInteger的值：4
当前线程Id:4
当前AtomicInteger的值：5
当前线程Id:5
当前AtomicInteger的值：6
当前线程Id:6
当前AtomicInteger的值：7
当前线程Id:7
当前AtomicInteger的值：8
当前线程Id:8
当前AtomicInteger的值：9
当前线程Id:9
当前AtomicInteger的值：10
```

> 可以看出来使用很简单，初始化值，通过{@code get} or {@code set}方法获取要绑定的值即可。那么为什么ThreadLocal可以实现这样的功能呢。我们分析一下他的源码。

### 源码分析

**set方法:**

```java
public void set(T value) {
        Thread t = Thread.currentThread();//获取当前的线程
        ThreadLocalMap map = getMap(t);   //根据当前线程获取该线程的ThreadLocalMap
        if (map != null)
            map.set(this, value);		  //如果这个map不等于空就把新加入的值和当前线程绑定
        else
            createMap(t, value);		  //如果不存在则创建这个map并把值绑定
    }
```

**createMap方法：**

```java
void createMap(Thread t, T firstValue) {
  		//很简单就是给当前线程生成一个ThreadLocalMap并且绑定初值
        t.threadLocals = new ThreadLocalMap(this, firstValue);
    }
```

**get方法：**

```java
public T get() {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null) {
            ThreadLocalMap.Entry e = map.getEntry(this); //map不空根据键取值
            if (e != null) {
                @SuppressWarnings("unchecked")
                T result = (T)e.value;
                return result;
            }
        }
        return setInitialValue(); //否则设置初始值并返回，若没有初始值则会返回空
    }
```

> 可以发现ThreadLocal真正起作用的其实是其中的ThreadLocalMap，所以当ThreadLocal被声明为static时，相关线程都可以访问同一份ThreadLocal对象，但是其中的值是和其线程绑定的，因此不会产生干扰。但是当ThreadLocal不被声明为static时，其实更好理解，它就是一个局部的只能存一个值key为当前线程的map变量，也就不存在其它线程访问的问题了。**ThreadLocal< T > 类似于包含了Map< Thread,T >对象，其中保存了特定于该线程的值，但其实现并非如此。这些特定于线程的值保存在Thread对象中，当线程终止后，这些值会作为垃圾回收。** 

**下面代码和最初的演示代码逻辑相同，但是输出结果不同，大家可以想想为什么。**

```java
public class d_ReorderingDemo {

    private static class ReaderThread extends Thread {

        private AtomicInteger threadId = new AtomicInteger(0);
        private ThreadLocal<Integer> threadLocal =
        ThreadLocal.withInitial(threadId::getAndIncrement);//这里只是λ表达式的初始化形式

        public long getId() {
            return threadLocal.get();
        }

        @Override
        public void run() {
            System.out.println("当前线程Id:" + threadLocal.get());
            System.out.println("当前AtomicInteger的值：" + threadId.get());
        }
    }

    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
            new ReaderThread().start();
        }
    }
}

//输出：=========================================
当前线程Id:0
当前AtomicInteger的值：1
当前线程Id:0
当前AtomicInteger的值：1
当前线程Id:0
当前AtomicInteger的值：1
当前线程Id:0
当前AtomicInteger的值：1
当前线程Id:0
当前AtomicInteger的值：1
当前线程Id:0
当前AtomicInteger的值：1
当前线程Id:0
当前AtomicInteger的值：1
当前线程Id:0
当前AtomicInteger的值：1
当前线程Id:0
当前AtomicInteger的值：1
当前线程Id:0
当前AtomicInteger的值：1
```

