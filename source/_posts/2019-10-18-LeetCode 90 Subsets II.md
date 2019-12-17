---
title: LeetCode-Subsets II
date: 2019-10-18 16:48:08
categories:
- [编程,算法]
tags:
- Leetcode
- 算法
---



## LeetCode: [Subsets II](https://leetcode.com/problems/subsets-ii/)

### 题目:

> 给定一个集合，找出该集合可能的所有子集，当前集合包含重复元素。

### 例子：

> ```java
> Input: [1,2,2]
> Output:
> [
>   [2],
>   [1],
>   [1,2,2],
>   [2,2],
>   [1,2],
>   []
> ]
> ```

### 思路:

> 这个题和subsets的思路大致一样，就是当前的元素到底要不要，不同点在于是否如何把相同元素的不同组合去重。首先就是对所查找集合进行排序，排序完成后相同的元素就在相邻的位置了，然后在查找的过程中，如果相同的靠前元素还没使用，当前元素就不应该纳入。

```java
class Solution {
  public List<List<Integer>> subsetsWithDup(int[] nums) {

        if (null == nums || nums.length == 0) {
            return new ArrayList<>();
        }

        List<List<Integer>> result = new ArrayList<>();
        boolean[] used = new boolean[nums.length];
        Arrays.sort(nums);

        findAllSubsetsWithDup(result, new ArrayList<Integer>(), nums, used, 0);

        return result;
    }

    private void findAllSubsetsWithDup(List<List<Integer>> result, List<Integer> tempSubset, int[] nums, boolean[] used, int start) {
        if (start == nums.length) {
            result.add(new ArrayList<>(tempSubset));
            return;
        }

        if (!used[start] && !(start > 0 && nums[start] == nums[start - 1] && !used[start - 1])) {
            used[start] = true;
            tempSubset.add(nums[start]);
            findAllSubsetsWithDup(result, tempSubset, nums, used, start + 1);
            tempSubset.remove(tempSubset.size() - 1);
            used[start] = false;
        }

        findAllSubsetsWithDup(result, tempSubset, nums, used, start + 1);
    }
}
```

