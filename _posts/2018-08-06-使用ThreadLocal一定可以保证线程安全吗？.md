# 使用ThreadLocal一定可以保证线程安全吗？

[TOC]

### 前言

> ThreadLocal大家可能或多或少接触过，多线程编程中也都把它作为实现线程安全的一种方式，至于实现细节不过多在这里讨论，主要是通过一个线程id和一个Map做对应，用空间换取线程安全的一种方式。所以在传统后端开发使用的过程中一般都用它来存储用户的一些信息，比如认证成功后就把用户的信息存入到ThreadLocal中，在后面的业务逻辑中可以取到对应的用户信息做相关操作。那么这样使用一定能保证线程安全吗？

### 问题发现

> 在一次项目的service中使用了`@Async`这样一个异步方法，方法中使用了ThreadLocal获取用户的信息，然而却出现了与预期不一致的结果。Spring的`@Async`的本质也是使用线程池管理这样的请求，从而达到异步的目的。

### 问题根源

> 到这儿其实对线程池比较了解的已经明白了应该，对于一个使用ThreadPoolExecutor类创建的线程池来讲，有线程闲置这样一个概念，新请求来了后不是为每一个请求创建一个新的线程，而是若当前池中有闲置的线程，则使用该闲置的线程。Web容器其实类似Jetty也是一样的会复用线程以提高效率。但是这个效率的提高和ThreadLocal的结合就会出现问题。
>
> 下面结合代码举例说明这个问题：
>
> ```java
> //测试所用的controller
>     @RequestMapping("/demo/async")
>     public Object asyncTest() {
>         demoService.funA();
>         demoService.funB(UserUtils.getUser());
>         return AjaxResultUtil.success(UserUtils.getUser());
>     }
> 
> 	@Async
>     @Override
>     public void funA() {
>         try {
>             System.out.println("===========funA() Begin===========");
>             //直接在异步方法中获取ThreadLocal中的对象
>             System.out.println(UserUtils.getUser());
>             System.out.println("===========funA() Begin===========");
>             Thread.sleep(5000);
>         } catch (InterruptedException e) {
>             e.printStackTrace();
>         }
>     }
> 
>     @Async
>     @Override
>     public void funB(User user) {
>         try {
>             Thread.sleep(1000);
>             System.out.println("===========funB() Begin===========");
>             //将ThreadLocal中的对象作为参数传递给异步方法
>             System.out.println(user);
>             System.out.println("===========funB() Begin===========");
>             Thread.sleep(1000);
>         } catch (InterruptedException e) {
>             e.printStackTrace();
>         }
>     }
> 
> /* 输出：
> ===========funA() Begin===========
> User{id=1, login='zhangsan', name='张三'}
> ===========funA() Begin===========
> ===========funB() Begin===========
> User{id=1, login='zhangsan', name='张三'}
> ===========funB() Begin===========
> 
> ===========funA() Begin===========
> User{id=1, login='zhangsan', name='张三'}
> ===========funA() Begin===========
> ===========funB() Begin===========
> User{id=2, login='lisi', name='李四'}
> ===========funB() Begin===========
> 
> */
> ```
>
> 

#### 操作流程

> 1. 使用张三登录访问上述接口
> 2. 注销张三用户，登录李四账户
> 3. 使用李四账户访问上述接口

#### 结果分析

> 可以看到第一次是正常的，但是这里有个细节，就是直接获取ThreadLocal还是可以获取值，这里分析ThreadLocal默认是继承自父线程的，所以会产生如上结果。
>
> 而第二次就不正常了，因为明明当前登录用户是李四，怎么还是张三呢，原因就在于这个线程复用的是第一次访问的线程。所以出现了问题。
>
> 至此，可以得出结论，线程安全还是要结合实际场景，具体问题具体分析。

