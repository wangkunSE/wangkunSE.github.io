## LeetCode: [Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/)

### 题目：

> 给一个数组，找出这个数组的最大子数组的乘积

### 例子:

> ```java
> Example 1:
> 
> Input: [2,3,-2,4]
> Output: 6
> Explanation: [2,3] has the largest product 6.
> Example 2:
> 
> Input: [-2,0,-1]
> Output: 0
> Explanation: The result cannot be 2, because [-2,-1] is not a subarray.
> ```

### 思路:

> 其实可以明显感觉出这个题和最大子数组和有很大关系，但是区别在于乘积的话如果有偶数个负数，之前最小的乘积也可能是最大的。因此这个题就需要多保存一个值，那就是最小值。

### 代码:

```java
class Solution {
    public int maxProduct(int[] A) {
        if (A.length == 0) {
            return 0;
        }

        int maxherepre = A[0];
        int minherepre = A[0];
        int maxsofar = A[0];
        int maxhere, minhere;

        for (int i = 1; i < A.length; i++) {
            maxhere = Math.max(Math.max(maxherepre * A[i], minherepre * A[i]), A[i]);
            minhere = Math.min(Math.min(maxherepre * A[i], minherepre * A[i]), A[i]);
            maxsofar = Math.max(maxhere, maxsofar);
            maxherepre = maxhere;
            minherepre = minhere;
        }
        return maxsofar;
    }
}
```

