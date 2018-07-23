## LeetCode:[Rotate Image](https://leetcode.com/problems/rotate-image/)

#### 题目：

> 将一个矩阵顺时针旋转90度，要求空间复杂度为`O(1)`

#### 例子:

> ```java
> Given input matrix = 
> [
>   [1,2,3],
>   [4,5,6],
>   [7,8,9]
> ],
> 
> rotate the input matrix in-place such that it becomes:
> [
>   [7,4,1],
>   [8,5,2],
>   [9,6,3]
> ]
> ```

#### 思路：

> 说来惭愧，这个题一开始拿到没思路，因为没有暴力方法，搁置几天后再看发现其实可以通过翻转来解题，先以反对角线为轴旋转，然后在水平翻转即为旋转90度的效果。
>
> ```java
> [1,2,3]	         [9,6,3]			[7,4,1]
> [4,5,6]   -->    [8,5,2]	-->		[8,5,2]
> [7,8,9]			 [7,4,1]			[9,6,3]
> ```

#### 代码：

> 做完居然是标答超过100%

```java
class Solution {
    public void rotate(int[][] matrix) {
        
        if( matrix == null || matrix.length < 1){
            return;
        }
        
        int rowLength = matrix.length;
        int colLength = matrix[0].length;
        for(int i = 0; i < rowLength - 1;i++ ){
            for(int j = 0; j < rowLength-i-1; j++){
                swap(matrix,i,j,colLength-j-1,rowLength-i-1);
            }
        }
        
        for(int i = 0; i < rowLength/2; i++){
            for(int j = 0; j < colLength; j++){
                swap(matrix,i,j,rowLength-i-1,j);
            }
        }
    }
    
    private void swap(int[][] nums,int fromX,int fromY,int toX,int toY){
        int temp = nums[fromX][fromY];
        nums[fromX][fromY] = nums[toX][toY];
        nums[toX][toY] = temp;
    }
}
```

