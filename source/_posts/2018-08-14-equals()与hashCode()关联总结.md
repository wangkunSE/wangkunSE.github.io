---
title: equals()与hashCode()关联总结
date: 2018-08-14 15:48:08
categories:
- [编程]
- [Java]
tags:
- Java
---

### 1. equals()

#### 1.1含义

> 默认代表两个对象的引用是否相等，等同于 ==。但是一般的覆盖行为是为了使两个对象逻辑相等。
>
> ```java
> Point point1 = new Point(0,0);
> Point point2 = new Point(0,0);
> point1.equals(point2);
> 
> //我们希望这个结果是相等的，因为他们在同一点上，尽管他们的对象引用不同。
> ```

#### 1.2 性质

- 自反性 ：非null情况下`x.equals(x)`永远返回true。
- 对称性 :  `x.equals(y) 和 y.equals(x)`的值相同
- 传递性：`x.equals(y) 为true  y.equals(z) 为true 那么 z.equals(x) 为true`
- 一致性：对于一个信息未变的对象调用equals()方法返回的结果一致。

### 2. hashCode()

#### 2.1含义

> 用于散列集合的使用。

#### 2.2 约定

> - 在应用程序的执行期间，只要equals()操作所用到的信息未发生变化，那么hashCode()的值应当一致。
> - 如果两个对象是equals()的，那么他们的hashCode()方法值一定相同。
> - 两个对象不是equals()的，那么他们的hashCode()方法值不一定不同。

#### 2.3 细节

##### 实现一个良好的hashCode()方法时采取的规约:

```java
/**
之所以每次乘31,是因为如果域值不同，但是和相同也会造成hash冲突，这样能保证值得顺序。
*/
@Override
public int hashCode(){
    //一个初始化的值，使hash值分布更均匀
    int result = 17;
    result = 31 * result + field1;
    result = 31 * result + field2;
    result = 31 * result + field3;
    ...
    result = 31 * result + fieldn;
    
    return result;
}
```

