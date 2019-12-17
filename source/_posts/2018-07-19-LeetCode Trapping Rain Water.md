---
title: LeetCode-Trapping Rain Water
date: 2018-07-19 16:48:08
categories:
- [编程,算法]
tags:
- Leetcode
- 算法
---



## LeetCode: [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)

#### 题目:

> 给一个不含负值的数组，求其最大的容水量。

#### 例子：

> 结合下图看
>
> ![img](../images/leetcode/rainwatertrap.png)
>
> ```java
> Input: [0,1,0,2,1,0,1,3,2,1,2,1]
> Output: 6
> ```

#### 思路:

> 这道题经历了很久才完成，主要是感觉很棘手，不好找切入点。后来借鉴了一个思路，就是每次找最高的点，然后找到次高的左右各一个点，以次高的点为容器边缘，算它们与最高点之间的容水量，然后通过递归，再找次高点之外的的存水量。

#### 代码:

> 有些丑陋，但是效率击败了96%的人。

```java
class Solution {
    public int trap(int[] height) {
        if( height == null || height.length < 3){
            return 0;
        }
        int total =  getTheTotalWater(height,0,height.length,0);
        return total;
    }

    private int getTheTotalWater(int[] height,int start,int end,int index){
        if (start < end - 1) {
            int containsLeft = 0;
            int containsRight = 0;
            //find current max value index
            int max = findTheMax(height,start,end,index);
            //find left second max value index
            int left = findTheMax(height,start,max,max);
            //find right second max value index
            int right = findTheMax(height,max+1,end,max);
            //left water capcity
            containsLeft = getTheWater(height,left,max,height[left]);
            if(right < end){
               //right water capcity
                containsRight = getTheWater(height,max,right+1,height[right]);
            }
            //find outward
            return getTheTotalWater(height,start,left+1,left)
                +getTheTotalWater(height,right,end,right)
                +containsLeft+containsRight;
        }
        return 0;
    }

    private int findTheMax(int[] nums,int start,int end,int prevMaxIndex){
        int max = Integer.MIN_VALUE;
        int index = start;
        int dist = 0;
        for(int i = start; i < end; i++){
            int tempDis = Math.abs(prevMaxIndex - i);
            if(max < nums[i] ){
                max = nums[i];
                index = i;
            }else if (max == nums[i] && dist < tempDis){
                index = i;
                dist = tempDis;
            }
        }
        return index;
    }

    private int getTheWater(int[] nums,int start,int end,int height){
        int total = 0;
        for(int i = start;i<end; i++){
            if(nums[i]<=height){
                total+=(height - nums[i]);
            }
        }
        return total;
    }
}
```

