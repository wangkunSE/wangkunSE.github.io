## Synchronized和Date的实践

> 作为Java使用者，对于synchronized肯定不陌生，它是多线程中重要的一个机制，能够保证多线程程序在共享数据时的正确性。但是最近遇到一个问题，久久没有想明白，查阅了很多博客和资料最终终于想明白了，决定写下来给大家参考。

**如果想了解synchronized的机制以及原理的话给大家推荐一个博客：**

[Java 之 synchronized 详解](https://juejin.im/post/594a24defe88c2006aa01f1c)

### 起因

```java
    private static void synchronizedDemo() {
        Task task = new Task();
        for (int i = 0; i < 10; i++) {
            new Thread(task).start();
        }
    }

    private static class Task implements Runnable {
        private static final String MY_LOCK_DATE = "date_lock";
        private Integer value = 0;
        private static SimpleDateFormat sdf = new SimpleDateFormat("HH:mm:ss|SSS");

        @Override
        public void run() {
            createOrder();
        }
        
        private void createOrder() {
            synchronized (MY_LOCK_DATE) {
                String format = sdf.format(new Date());
                System.out.println(Thread.currentThread().getName() + "生成的订单号是：" + format);   
            }
        }
    }
```

> 对于这段代码，想必大家大概能看出我的意图，我想用当前时间来生成唯一的订单号，这里是模拟的业务场景，所以生成订单号一定是在多线程场景下的。所以这里我使用了一个`MY_LOCK_DATE`锁来保证线程访问的唯一性。然而最后的输出结果是这样的：

```java
Thread-3生成的订单号是：11:29:49|357
Thread-7生成的订单号是：11:29:49|358
Thread-2生成的订单号是：11:29:49|358
Thread-4生成的订单号是：11:29:49|358
Thread-6生成的订单号是：11:29:49|358
Thread-8生成的订单号是：11:29:49|358
Thread-10生成的订单号是：11:29:49|358
Thread-1生成的订单号是：11:29:49|370
Thread-5生成的订单号是：11:29:49|370
Thread-9生成的订单号是：11:29:49|370
```

### 经过

**这怎么行，不管在什么场景中订单号一定是唯一的！难道是锁没有起作用？修改一下代码，看看到底是不是锁的问题：**

```java
private static class Task implements Runnable {
        private static final String MY_LOCK_DATE = "date_lock";
        private Integer value = 0;
        private static SimpleDateFormat sdf = new SimpleDateFormat("HH:mm:ss|SSS");

        @Override
        public void run() {
            createOrder();
        }

        private void createOrder() {
            synchronized (MY_LOCK_DATE) {
                String format = sdf.format(new Date());
                System.out.println(Thread.currentThread().getName() + "生成的订单号是：" + format);
                value++;
                System.out.println(Thread.currentThread().getName() + "当前的value值为：" + value);
            }
        }
    }
```

**这里就是加入了一个value变量，每生成一个订单就让其加一并输出。看下结果：**

```java
Thread-1生成的订单号是：11:32:20|270
Thread-1当前的value值为：1
Thread-7生成的订单号是：11:32:20|271
Thread-7当前的value值为：2
Thread-9生成的订单号是：11:32:20|271
Thread-9当前的value值为：3
Thread-5生成的订单号是：11:32:20|271
Thread-5当前的value值为：4
Thread-10生成的订单号是：11:32:20|271
Thread-10当前的value值为：5
Thread-6生成的订单号是：11:32:20|271
Thread-6当前的value值为：6
Thread-8生成的订单号是：11:32:20|271
Thread-8当前的value值为：7
Thread-4生成的订单号是：11:32:20|271
Thread-4当前的value值为：8
Thread-3生成的订单号是：11:32:20|271
Thread-3当前的value值为：9
Thread-2生成的订单号是：11:32:20|271
Thread-2当前的value值为：10
```

**说明锁确实起作用了，但是为什么我的订单号还是一片重复，损失上亿不说，我可是要做Java王的男人啊，这点问题都搞不定嘛。于是我灵光一闪，发现可能是Date的问题。扫了一眼源码，发现其就是`System.currentTimeMillis();`的封装啊。**

### 结果

> 到这里其实问题就解决了，这个系统时间的方法并不是线程安全的，各个线程任何时刻都可以获取到，但是可能有人问即使它不安全，但是我加锁了呀。确实，而且可以看到锁也生效了，但是要知道处理器的执行速度非常快，那几个订单号的生成加线程切换可能不足1毫秒。所以怎么解决这个问题呢？看最终代码：

```java
private static class Task implements Runnable {
        private static final String MY_LOCK_DATE = "date_lock";
        private static Integer value = 0;
        private static SimpleDateFormat sdf = new SimpleDateFormat("HH:mm:ss|SSS");

        @Override
        public void run() {
            createOrder();
        }

        private void createOrder() {
            synchronized (MY_LOCK_DATE) {
                try {
                    Thread.sleep(1); //让该线程睡1毫秒
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.currentTimeMillis();
                String format = sdf.format(new Date());
                System.out.println(Thread.currentThread().getName() + "生成的订单号是：" + format);
                value++;
                System.out.println(Thread.currentThread().getName() + "当前的value值为：" + value);
            }
        }
    }

//输出===================================
Thread-3生成的订单号是：11:45:35|147
Thread-3当前的value值为：1
Thread-10生成的订单号是：11:45:35|148
Thread-10当前的value值为：2
Thread-6生成的订单号是：11:45:35|149
Thread-6当前的value值为：3
Thread-2生成的订单号是：11:45:35|150
Thread-2当前的value值为：4
Thread-8生成的订单号是：11:45:35|151
Thread-8当前的value值为：5
Thread-7生成的订单号是：11:45:35|153
Thread-7当前的value值为：6
Thread-4生成的订单号是：11:45:35|156
Thread-4当前的value值为：7
Thread-9生成的订单号是：11:45:35|157
Thread-9当前的value值为：8
Thread-5生成的订单号是：11:45:35|158
Thread-5当前的value值为：9
Thread-1生成的订单号是：11:45:35|159
Thread-1当前的value值为：10
```

细心的同学已经发现了就是让持有锁的线程睡一毫秒。诶呀，结果也很漂亮，基本都是挨着的订单号。但是肯定有人说这样好麻烦啊，是的，所以个人建议**最好不要利用Date来作为任何一个主键**，而且**在多线程的环境中，使用Date一定要小心。**不然很容易出现意想不到的结果。那么有的人说我就要用Date怎么办？那么你可以去了解一下**分布式锁**，这个很好的解决了这个问题。

### 最后

**给大家出一个小题，看看这个程序的输出：**

```java
   private static void synchronizedDemo() {
        for (int i = 0; i < 10; i++) {
            new Thread(new Task()).start();
        }
    }

    private static class Task implements Runnable {
        private static final String MY_LOCK_DATE = "date_lock";
        private Integer value = 0;
        private static SimpleDateFormat sdf = new SimpleDateFormat("HH:mm:ss|SSS");

        @Override
        public void run() {
            createOrder();
        }

        private void createOrder() {
            synchronized (MY_LOCK_DATE) {
                try {
                    Thread.sleep(1);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                String format = sdf.format(new Date());
                System.out.println(Thread.currentThread().getName() + "生成的订单号是：" + format);
                value++;
                System.out.println(Thread.currentThread().getName() + "当前的value值为：" + value);
            }
        }
    }
```

