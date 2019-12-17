---
title: LeetCode-House Robber
date: 2018-08-10 13:48:08
categories:
- [编程,算法]
tags:
- Leetcode
- 算法
---



## LeetCode:[House Robber](https://leetcode.com/problems/house-robber/)

### 题目：

> 给你一个数组，从中取值，不能连续取相邻的值，求这个数组可取的最大值。

### 例子:

> ```java
> Example 1:
> 
> Input: [1,2,3,1]
> Output: 4
> Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
>              Total amount you can rob = 1 + 3 = 4.
> Example 2:
> 
> Input: [2,7,9,3,1]
> Output: 12
> Explanation: Rob house 1 (money = 2), rob house 3 (money = 9) and rob house 5 (money = 1).
>              Total amount you can rob = 2 + 9 + 1 = 12.
> 
> ```

### 思路:

> 这个题若想暴力求解情况是很复杂的，因为要考虑要当前的值，就不能要左右的值。若用DP的思维，这个题就很简单了，就考虑当前的值拿不拿，而拿与不拿取决于之前不拿的最大值和当前值的和与上一个值拿的值哪个大。

### 代码:

> 可以看到代码十分简洁，dp的题都是这样。

```java
class Solution {
    public int rob(int[] nums) {
        if(nums == null || nums.length < 1){
            return 0;
        }
        
        int rob = 0;
        int notRob = 0;
        for(int i = 0; i < nums.length;i++){
            int currentRob = rob;
            //拿当前的值
            rob = Math.max(notRob+nums[i],rob);
            //不拿当前的值
            notRob = Math.max(currentRob,notRob);
        }
        
        return Math.max(rob,notRob);
    }
}
```

