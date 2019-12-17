---
title: LeetCode-Subset
date: 2019-09-26 16:48:08
categories:
- [编程,算法]
tags:
- Leetcode
- 算法
---



## LeetCode: [Subset](https://leetcode.com/problems/subsets/)

### 题目:

> 给定一个不重复的整型列表，要求返回它所有的子集

### 例子：

> ```java
> Input: nums = [1,2,3]
> Output:
> [
>   [3],
>   [1],
>   [2],
>   [1,2,3],
>   [1,3],
>   [2,3],
>   [1,2],
>   []
> ]
> ```

### 思路:

> 好久没做题了，一碰到这个题隐隐约约感觉会做，但是感觉非常卡顿。最后受到启发，其实整体就是单看每一个数字拿与不拿的问题，这里就有似曾相识的感觉了。

### 代码：

```java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        if (null == nums || nums.length < 1) {
            return new ArrayList<>();
        }

        List<List<Integer>> result = new ArrayList<>();
        List<Integer> localSet = new ArrayList<>();
        result.add(new ArrayList<>());

        fillResultForNums(result, nums, localSet, 0);
        return result;
    }

    private void fillResultForNums(List<List<Integer>> result, int[] nums, List<Integer> localSet, int offset) {
        if (offset >= nums.length) {
            return;
        }

        //pick
        int val = nums[offset];
        localSet.add(val);
        fillResultForNums(result, nums, localSet, offset + 1);
        result.add(new ArrayList<>(localSet));

        //not pick
        localSet.remove(localSet.size() - 1);
        fillResultForNums(result, nums, localSet, offset + 1);
    }
}
```

