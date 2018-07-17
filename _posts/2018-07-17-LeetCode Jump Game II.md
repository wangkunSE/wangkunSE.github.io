## LeetCode : Jump Game II

#### 题目：

> 给出一个只含正数的数组，一开始你就在数组的第一个索引上，每个元素代表你能跳的最大步数，你的目标是用最小的跳数跳到数组末尾。保证不会存在不能跳到数组末尾的情况（不保证的话可以判断一下就是JUMP GAME I）。

#### 例子：

>

#### 思路:

> 显然这是一个贪心算法，从后往前看，每次获取到最靠前的索引值，然后用这个索引值进行迭代就可以解题。但是我这么做时间复杂度是O（n^2），并没有发挥贪心的特性。

#### 解法一：

```java
class Solution {
    public int jump(int[] nums) {
            if(nums== null || nums.length < 2){
                return 0;
            }
            int count = 0;
            int length = nums.length;
            int lastPos = length -1;
            int minIndex = length -1;

            for(int i = length - 2; i >= 0;){
                for(int j = i ; j >= 0; j--){
                    if(nums[j] + j >= lastPos && minIndex > j){
                        minIndex = j;
                    }
                }
                lastPos = minIndex;
                if(minIndex >= 0){
                    count ++;
                }
                if (minIndex == 0){
                    break;
                }
                i = minIndex;
            }
            return count;
        }
}
```

#### 解法二：

> 正宗贪心 O(n)的复杂度，十分漂亮

```java
class Solution {
    public int jump(int[] nums) {
            if(nums== null || nums.length < 2){
                return 0;
            }
            
            int count = 0;
            int length = nums.length;
            int nextMax = 0;
            int curMax = 0;

            for(int i = 0; i < length -1; i++){
                nextMax = Math.max(nextMax,nums[i]+i);
                if(i == curMax){
                    count ++;
                    curMax = nextMax;
                }
            }
            return count;
        }
}
```

#### 解法三：

> Dp 可以获取到每一个元素的最少跳数，且自带判断是否可达最后一个元素O(n^2)的复杂度

```java
class Solution {
    public int jump(int[] nums) {
        if (nums == null || nums.length == 0)
            return 0;
        int[] dp = new int[nums.length];
        dp[nums.length-1] = 0;
        
        for (int i = nums.length-2; i >= 0; i--){
            int curr = nums.length;
            for (int j = 1; j <= nums[i]; j ++) {
                if (i + j <= nums.length-1) {
                    curr = Math.min(curr, dp[i+j]);
                }
            }
            dp[i] = curr+1;
        }
        
        return dp[0];
    }
}
```

#### 解法四：

> BFS 的思想，找出每层能达到的最远的地方，然后从那个地方开始往下继续找。

```java
int jump(int A[], int n) {
	 if(n<2)return 0;
	 int level=0,currentMax=0,i=0,nextMax=0;

	 while(currentMax-i+1>0){		//nodes count of current level>0
		 level++;
		 for(;i<=currentMax;i++){	//traverse current level , and update the max reach of next level
			nextMax=max(nextMax,A[i]+i);
			if(nextMax>=n-1)return level;   // if last element is in level+1,  then the min jump=level 
		 }
		 currentMax=nextMax;
	 }
	 return 0;
 }
```

