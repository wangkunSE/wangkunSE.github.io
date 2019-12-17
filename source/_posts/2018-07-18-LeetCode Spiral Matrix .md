---
title: LeetCode-Spiral Matrix
date: 2018-07-18 16:37:08
categories:
- [编程,算法]
tags:
- Leetcode
- 算法
---



## LeetCode: Spiral Matrix

#### 题目：

> 顺时针打印矩阵

#### 例子：

> ```java
> Example 1:
> Input:
> [
>  [ 1, 2, 3 ],
>  [ 4, 5, 6 ],
>  [ 7, 8, 9 ]
> ]
> Output: [1,2,3,6,9,8,7,4,5]
> 
> Example 2:
> Input:
> [
>   [1, 2, 3, 4],
>   [5, 6, 7, 8],
>   [9,10,11,12]
> ]
> Output: [1,2,3,4,8,12,11,10,9,5,6,7]
> ```

#### 思路：

> 就是从左至右，从上至下，从右至左，从下至上进行打印矩阵即可，但是这里要注意只剩1行和1列的处理

#### 代码:

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> result = new ArrayList<>();
        if(matrix == null || matrix.length == 0){
            return result;
        }
        
        int rowBegin = 0;
        int rowEnd = matrix.length - 1;
        int colBegin = 0;
        int colEnd = matrix[0].length -1;
        
        while(rowBegin <= rowEnd && colBegin <= colEnd){
            for(int j = colBegin; j <= colEnd; j++){
                result.add(matrix[rowBegin][j]);
            }
            rowBegin ++;
            
            for(int i = rowBegin; i <= rowEnd ; i++){
                result.add(matrix[i][colEnd]);
            }
            colEnd -- ;
            
            if(rowBegin <= rowEnd){
                for(int j = colEnd ; j >= colBegin ;j-- ){
                    result.add(matrix[rowEnd][j]);
                }
            }
            rowEnd --;
            
            if(colBegin <= colEnd){
                for(int i = rowEnd ; i >= rowBegin; i--){
                    result.add(matrix[i][colBegin]);
                }
            }
            colBegin ++;
        }
        
        return result;
        
    }
}
```

