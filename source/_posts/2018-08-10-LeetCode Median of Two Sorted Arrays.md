---
title: LeetCode-Median of Two Sorted Arrays
date: 2018-08-10 15:48:08
categories:
- [编程,算法]
tags:
- Leetcode
- 算法
---



## LeetCode:[Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/)

### 题目：

> 给两个排过序的数组，找出两个数组所有元素的中位数，要求时间复杂度为O(log(m+n))。

### 例子：

> ```java
> Example 1:
> 
> nums1 = [1, 3]
> nums2 = [2]
> 
> The median is 2.0
> Example 2:
> 
> nums1 = [1, 2]
> nums2 = [3, 4]
> 
> The median is (2 + 3)/2 = 2.5
> ```

### 思路：

> 其实这个题要是没有时间复杂度的限制很简单，但是有了这个时间复杂度的限制，那么可以看到遍历都是无法解决这个问题的，既然是排过序那么首先想到二分查找，但是怎么找到中位数呢。
>
> 
>
> ```java
> 这里就要说下中位数的概念了，一个数组arr的中位数代表什么？代表了
> 1. len(0~i-1) == len(i~arr.length-1)
> 2. arr[i-1] <= arr[i]
> 
> 因此转换成两个数组这个性质也是不变的，下面讨论两个有序数组A,B
> A[0]...A[i-1] | A[i]...A[m-1]
> B[0]...B[j-1] | B[j]...B[n-1]
> ----left------| ----right----
> 有了如下的等式：
> 1.i-1+j-1 = m-1+n-1 - i - j  ===> j = (m+n+1)/2-i; (这里为什么加一，是因为要上取整)
> 	即满足这个等式就可以使len(left) == len(right).
> 2.现在需要满足的就是让A[i-1] <= B[j] && B[j-1] <= A[i] 就可以找出中位数了。
> 
> 还有一点要注意的就是要保证 n>m 这样j才不会取到负值
> 
> ```

### 代码:

```java
class Solution {
    public double findMedianSortedArrays(int[] A, int[] B) {
            int[] longArr = null;
            int[] shortArr = null;
            if( A.length <= B.length){
                longArr = B;
                shortArr = A;
            }else{
                longArr = A;
                shortArr = B;
            }

            int m = shortArr.length,n = longArr.length;
            if(n < 1){
                return 0;
            }
            int imin = 0,imax = m,halfLen = (m+n+1)/2;
            while(imin <= imax ){
                int i = (imin + imax)/2;
                int j = halfLen - i;
                if(i > 0 && j < n && shortArr[i-1] > longArr[j] ){
                    imin = i - 1;
                }else if( j > 0 && i < m && longArr[j-1] > shortArr[i]){
                    imin = i + 1;
                }else{
                    double leftMax = 0.0;
                    if(i == 0){
                        leftMax = longArr[j-1];
                    }else if(j == 0){
                        leftMax = shortArr[i-1];
                    }else{
                        leftMax = Math.max(shortArr[i-1],longArr[j-1]);
                    }
                    if( (m + n) % 2 == 1){
                        return leftMax * 1.0;
                    }

                    double rightMin = 0.0;
                    if(i == m){
                        rightMin = longArr[j];
                    }else if(j == n){
                        rightMin = shortArr[i];
                    }else{
                        rightMin = Math.min(shortArr[i],longArr[j]);
                    }
                    return  (leftMax + rightMin)/2;
                }
            }
            return 0.0;
        }
}
```

