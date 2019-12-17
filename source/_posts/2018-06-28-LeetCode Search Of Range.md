---
title: LeetCode-Search for a Range
date: 2018-06-28 16:37:05
categories:
- [编程,算法]
tags:
- Leetcode
- 算法
---

## LeetCode: Search for a Range

#### 题目：

> 给出一个递增数组，找到`target`的开始和结束为止并返回。复杂度必须为`O(logn)`，如果没找到返回`[-1,-1]`;

#### 例子:

> ```java
> //例一
> Input: nums = [5,7,7,8,8,10], target = 8
> Output: [3,4]
> 
> //例二
> Input: nums = [5,7,7,8,8,10], target = 6
> Output: [-1,-1]
> ```

#### 思路：

> 我的思路是既然是递增序列，肯定首选二分查找，关键在于找边界，其实也不难，找到其中一个值，向两边顺序找左边界和右边界，返回即可。不过最坏的时间复杂度是O(n/2)，即整个数组全为同一个值的情况。但是实际运行效果还是超过了80%的人。

#### 代码：

```java
 public int[] searchRange(int[] nums, int target) {
        if (nums == null || nums.length < 1) {
            return new int[]{-1, -1};
        }
        int length = nums.length;
        int index = binarySearch(nums, 0, length - 1, target);
        if (index == -1) {
            return new int[]{-1, -1};
        }
        int temp = index;
        int left, right;
        while (temp >= 0 && nums[index] == nums[temp]) temp--;
        left = temp + 1;
        temp = index;
        while (temp < length && nums[index] == nums[temp]) temp++;
        right = temp - 1;

        return new int[]{left, right};
    }

    private int binarySearch(int[] arr, int start, int end, int target) {
        int low = start, high = end;
        while (low <= high) {
            int mid = (high - low) / 2 + low;
            if (arr[mid] == target) {
                return mid;
            } else if (arr[mid] > target) {
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return -1;
    }
```

#### 一个很有技巧性的解法:

```java
public class Solution {
	public int[] searchRange(int[] A, int target) {
		int start = Solution.firstGreaterEqual(A, target);
		if (start == A.length || A[start] != target) {
			return new int[]{-1, -1};
		}
		return new int[]{start, Solution.firstGreaterEqual(A, target + 1) - 1};
	}

	//find the first number that is greater than or equal to target.
	//could return A.length if target is greater than A[A.length-1].
	//actually this is the same as lower_bound in C++ STL.
	private static int firstGreaterEqual(int[] A, int target) {
		int low = 0, high = A.length;
		while (low < high) {
			int mid = low + ((high - low) >> 1);
			//low <= mid < high
			if (A[mid] < target) {
				low = mid + 1;
			} else {
				//should not be mid-1 when A[mid]==target.
				//could be mid even if A[mid]>target because mid<high.
				high = mid;
			}
		}
		return low;
	}
}
```

