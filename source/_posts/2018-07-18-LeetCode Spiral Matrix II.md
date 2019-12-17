---
title: LeetCode-Spiral Matrix II
date: 2018-07-18 16:38:08
categories:
- [编程,算法]
tags:
- Leetcode
- 算法
---



## LeetCode: Spiral Matrix II

#### 题目：

> 输入一个n，返回它的平方的一个顺时针排列的矩阵

#### 例子：

> ```java
> Input: 3
> Output:
> [
>  [ 1, 2, 3 ],
>  [ 8, 9, 4 ],
>  [ 7, 6, 5 ]
> ]
> ```

#### 思路：

> 同顺时针打印矩阵一样，甚至要简单一些，因为不会存在只剩1行，1列的情况

#### 代码:

```java
class Solution {
    public int[][] generateMatrix(int n) {
        if(n == 0){
            return null;
        }
        int[][] result = new int[n][n];
        
        int rowBegin = 0;
        int rowEnd = n - 1;
        int colBegin = 0;
        int colEnd = n - 1;
        int index = 1;
        
        while( rowBegin <= rowEnd && colBegin <= colEnd){
            for(int j=colBegin; j <= colEnd ; j++){
                result[rowBegin][j] = index++;
            }
            rowBegin ++;
            
            for(int i=rowBegin; i<=rowEnd; i++ ){
                result[i][colEnd] = index++;
            }
            colEnd--;
            
            for(int j=colEnd; j>=colBegin; j--){
                result[rowEnd][j] = index++;
            }
            rowEnd--;
            
            for(int i=rowEnd; i>=rowBegin; i--){
                result[i][colBegin] = index++;
            }
            colBegin++;
        }
        return result;
    }
}
```

