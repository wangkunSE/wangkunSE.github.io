## LeetCode: Find the Duplicate Number

#### 题目:

> 一个`n+1`长度的数组，其中的值为`1~n`，所以肯定有一个重复的值，找出这个重复的值，并且只用`O(n)`的时间复杂度，`O(1)`的空间复杂度

#### 例子：

> ```java
> Input: [1,3,4,2,2]
> Output: 2
> ```

#### 思路：

> 这个题是当时小米面试我的一道题，当时思路已经想出来了，但是代码没写出来，很遗憾。思路就是把当前的值不在对应的下标时则替换，如果当前的值等于对应下标的值，也就说明重复了，返回即可。

#### 代码:

```java
class Solution {
    public int findDuplicate(int[] nums) {
        if( nums == null || nums.length < 1){
            return 0;
        }
        
        int i = 0;   
        while(nums[i] != i){
            if(nums[nums[i]] == nums[i]){
                return nums[i];
            }
            swap(nums,i,nums[i]);
        }      
        return 0;
    }
    
    private void swap(int[] nums , int i, int j){
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

