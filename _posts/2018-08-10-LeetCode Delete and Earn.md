## LeetCode: [Delete and Earn](https://leetcode.com/problems/delete-and-earn/)

### 题目:

> 给一个数组，可以有这样的操作，如果你拿了nums[i]的值，则你现在有nums[i]的积分，但是你要把数组中值为nums[i]-1或nums[i]+1的值都删掉。返回一个该数组拿值得到最大积分的方式。

### 例子：

> ```java
> Example 1:
> Input: nums = [3, 4, 2]
> Output: 6
> Explanation: 
> 拿4所以3被删掉了，而还剩2，所以最终为6
> 
> Example 2:
> Input: nums = [2, 2, 3, 3, 3, 4]
> Output: 9
> Explanation: 
> 如果拿所有的2的话，因为要删掉所有的3，拿4同理，整个数组最大值为8；拿所有的三个话，2，4被删，整个数组最大的值为9，所以是9
> ```

### 思路:

> 这个题乍一看很花，但是隐隐觉得和HouseRobber有一些关系，想来想去感觉把相同的值加起来不能取相邻的不就是HouseRobber嘛。但是这里少考虑了数组中不止存在相邻的值。所以这个做法行不通。但是换个思路想，因为限制了nums[i]的最大值，所以我们可以结合一个桶，将所有相同的值归类，不取相邻的值，即转换成了HouseRobber的题。

### 代码：

> 依然十分简洁，可以看到主体和HouseRobber完全一样。所以要识别出同类的题并转化也是一中做题的好方式。

```java
class Solution {
    public int deleteAndEarn(int[] nums) {
        if(nums== null || nums.length<1){
            return 0;
        }
        int max = 0;
        for (int i : nums) {
            max = Math.max(i, max);
        }
        int[] val = new int[max+1];
        for(int i=0; i<nums.length ; i++){
            val[nums[i]]++;
        }
        
        int skip = 0;
        int notSkip = 0;
        for(int i = 1; i < max+1; i++){
            int cur = notSkip;
            notSkip = Math.max(val[i]*i+skip,notSkip);
            skip = Math.max(cur,skip);
        }
        
        return Math.max(skip,notSkip);
    }
}
```

