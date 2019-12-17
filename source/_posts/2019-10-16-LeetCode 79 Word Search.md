---
title: LeetCode-Word Search
date: 2019-10-16 16:48:08
categories:
- [编程,算法]
tags:
- Leetcode
- 算法
---



## LeetCode: [Word Search](https://leetcode.com/problems/word-search/)

### 题目:

> 给定一个二维字符数组和一个单词，找在字符数组中是否能够拼成当前单词。

### 例子：

> ```java
> board =
> [
>   ['A','B','C','E'],
>   ['S','F','C','S'],
>      ['A','D','E','E']
>    ]
>    
>    Given word = "ABCCED", return true.
>    Given word = "SEE", return true.
> Given word = "ABCB", return false.
> ```

### 思路:

> 这个题很明显是一个dfs的题，找到开始位置然后分别找上下左右看能否拼成当前单词就可以判断当前结果，需要注意的是找的过程中不能再使用已经使用过的字符。

### 代码：

**DFS**

```java
class Solution {
  public boolean exist(char[][] board, String word) {

        if (board == null || word == null) {
            return false;
        }

        char[] chars = word.toCharArray();
        boolean[][] used = new boolean[board.length][board[0].length];
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                if (chars[0] == board[i][j]) {
                    boolean exist = findExist(i, j, chars, board, 0, used);
                    if (exist) {
                        return true;
                    }
                }
            }
        }


        return false;
    }

    private boolean findExist(int row, int column, char[] chars, char[][] board, int offset, boolean[][] used) {
        if (offset >= chars.length) {
            return true;
        }

        if (row >= board.length || column >= board[0].length || row < 0 || column < 0 || used[row][column]) {
            return false;
        }


        if (chars[offset] != board[row][column]) {
            return false;
        }

        used[row][column] = true;
        boolean left = findExist(row, column - 1, chars, board, offset + 1, used);
        boolean top = findExist(row + 1, column, chars, board, offset + 1, used);
        boolean right = findExist(row, column + 1, chars, board, offset + 1, used);
        boolean down = findExist(row - 1, column, chars, board, offset + 1, used);
        used[row][column] = false;

        return right || down || top || left;


    }
}
```

