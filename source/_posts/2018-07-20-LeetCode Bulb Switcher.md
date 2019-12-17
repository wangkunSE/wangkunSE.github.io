---
title: LeetCode-Bulb Switcher
date: 2018-07-20 16:48:08
categories:
- [编程,算法]
tags:
- Leetcode
- 算法
---



## LeetCode: [Bulb Switcher](https://leetcode.com/problems/bulb-switcher/)

#### 题目:

> 初始时有 *n* 个灯泡关闭。 第 1 轮，你打开所有的灯泡。 第 2 轮，每两个灯泡你关闭一次。 第 3 轮，每三个灯泡切换一次开关（如果关闭则开启，如果开启则关闭）。第 *i* 轮，每 *i* 个灯泡切换一次开关。 对于第 *n* 轮，你只切换最后一个灯泡的开关。 找出 *n* 轮后有多少个亮着的灯泡。

#### 例子:

> ```java
> Input: 3
> Output: 1 
> Explanation: 
> At first, the three bulbs are [off, off, off].
> After first round, the three bulbs are [on, on, on].
> After second round, the three bulbs are [on, off, on].
> After third round, the three bulbs are [on, off, off]. 
> 
> So you should return 1, because there is only one bulb is on.
> ```

#### 思路:

> 一开始我想的是就根据他的描述做，就是每一轮toggle一次当前下标及其倍数的数组位置的值。但是这个思路会超时。但这个思路的算法复杂度其实为`O(nlogn)`，所以说明这道题只可以遍历一遍，后来发现其实每次输出是固定有规律的，每`2i+1`个值为一组，含一个1。下面是AC代码。

#### 代码:

```java
class Solution {
    public int bulbSwitch(int n) {
        int result = 0;
        int i = 1;
        while(n > 0){
            n = n - (2*i+1);
            result++;
            i++;
        }
        return result;
    }
}
```

#### 其他人的答案：

> 有些费解，但是看来找到一种求开平方的方法。

```java
class Solution {
    public int bulbSwitch(int n) {
        return (int)Math.sqrt(n);
    }
}
```

