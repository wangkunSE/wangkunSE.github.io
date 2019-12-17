---
title: SpringCloud系列（一）之Eureka
date: 2019-10-17 18:51:28
categories: [编程,springCloud]
tags:
- springCloud
---



> 前言：时间久了发现自己的记性真的越来越差了，很多当时记得非常清楚的东西，隔了很长时间发现有印象是有印象，但是模模糊糊，提起来可能驴唇不对马嘴。因此觉得有些东西还是要落实，要输出，这样自己的印象才会深刻，所以从现在开始学习的东西都尝试着总结，以便以后忘记时也能回来看看迅速得回忆起这些知识，温故而知新。



### 1. Eureka简介

> Eureka是Netflix开发的服务发现框架，本身是一个基于REST的服务，主要用于定位运行在AWS域中的中间层服务，以达到负载均衡和中间层服务故障转移的目的。SpringCloud将它集成在其子项目spring-cloud-netflix中，以实现SpringCloud的服务发现功能。
>
> **概括一下就是Eureka是一种注册中心**

### 2. Eureka架构

{% asset_img arch.jpg %}



> 可以看到整个架构中有三种角色**Eureka Server 、Service Consumer 、Service Provider**。分别对应了注册中心，服务消费者，服务提供者。服务提供者将自己的信息注册到注册中心，并且可以更新和取消该注册。注册中心间同步服务提供者的信息。服务消费者通过注册中心找到服务提供者然后发起远程调用。

### 3. Eureka工作流程

#### 3.1 获取服务提供者信息

> 每个服务在EurekaServer中是以**InstanceInfo**的形式存储在registry的map中的，map的具体key和value如下图所示。

{% asset_img datastructure.png %}

> 客户端通过应用的名称获取相应的应用信息，然后发起远程调用。为了提高效率EurekaServer提供了二级缓存，具体的缓存如下图所示。

{% asset_img cache.png %}

> 可以看到总共有两级缓存，一个是只读缓存，提供给客户端用的，另一个利用guava实现的读写缓存，并且每30S就会同步到只读缓存中。并且会有定时任务检查实例的过期时间从而剔除不需要的服务并且更新registry和读写缓存。缓存获取的逻辑就是客户端查找一个实例的信息，若只读缓存中没有当前信息的话从读写缓存中去查找，若读写缓存中也没有的话才回去registry中查找。

#### 3.2 注册客户端信息

{% asset_img updateMetadata.png %}

> 如上图所示为注册信息入口，可以看到是一个http请求。从这个方法中找到下图方法为详细注册方法。

{% asset_img register.png %}

> 可以看到显示到父类执行了注册信息，然后将信息同步注册到了其他的server中。

{% asset_img registertopeer.png %}

> 看到最后就是将注册信息同步到其他server中。

#### 3.3 数据更新同步

{% asset_img renewlease.png %}

> 更新数据以及状态使用的是续约接口，可以看到也是registry的一个叫renew的方法。

{% asset_img renew.png %}

> 如上图所示，更新也是先到父类执行更新方法，最后将更新信息同步到其他的server上，而更新时比对的就是当前的实例信息的修改时间和需要更改信息的修改时间，时间靠后的取胜。可以看到Eureka其实并不能实时保证server间数据是一致的。

### 4. 注册中心

> 作为注册中心来讲，就是服务消费者找到服务提供者的一个通讯录，当一个服务消费者已经找到服务提供者时，即便通讯录消失了，也不应当使服务消费者找不到之前联系过的提供者。也就是说注册中心要做到系统的独立性，系统对注册中心的依赖应当非常小。第二就是注册中心不应当因为自己的原因保证不了自己在任何时刻都是可用的状态。而Eureka这两点全部都做到了，所以是一个合格的注册中心。

{% asset_img zkvseureka.png %}

> 上图是Eureka和zk的对比，可以看出它与zk不同的部分正是它更适合做注册中心的关键。
>
> 但是遗憾的是Eureka的2.x及以上版本目前已经不提供开源了，不过目前spring cloud使用阿里的nacas作为注册中心。后面再对它进行分析。

### 参考资料

> https://blog.csdn.net/zhxdick/article/list/2
>
> https://www.infoq.cn/article/why-doesnot-alibaba-use-zookeeper
>
> https://medium.com/knerd/eureka-why-you-shouldnt-use-zookeeper-for-service-discovery-4932c5c7e764
>
> https://github.com/apache/zookeeper
>
> https://github.com/Netflix/eureka