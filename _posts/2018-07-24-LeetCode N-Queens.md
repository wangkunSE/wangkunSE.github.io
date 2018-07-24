## LeetCode: [N-Queens](https://leetcode.com/problems/n-queens/)

#### 题目:

> 输入一个数字n，输出一个n*n大小的棋盘能放下n个皇后的所有情况的集合。

#### 例子：

> ```java
> Input: 4
> Output: [
>  [".Q..",  // Solution 1
>   "...Q",
>   "Q...",
>   "..Q."],
> 
>  ["..Q.",  // Solution 2
>   "Q...",
>   "...Q",
>   ".Q.."]
> ]
> 解释：对于4皇后来说有以上两种独特的解法
> ```

#### 思路:

> 不难想象解这个题需要回溯法，每一行只可能有一个Queen，所以通过行来找，每一行放置一个Q即结束。我的解法思路和之前解数独问题的解法一致。

#### 代码:

> 虽然代码思路差不多，但是有很多细节也比较麻烦，比如判断这个地方能否放置Queen。

```java
class Solution {
   public List<List<String>> solveNQueens(int n) {
            List<List<String>> result = new ArrayList<>();
            if(n<1){
                return result;
            }
            char[][] chess = new char[n][n];
            for (int i = 0; i < chess.length; i++) {
                for (int j = 0; j < chess.length; j++) {
                    chess[i][j] = '.';
                }
            }
            backtrace(result, n, 0, chess);
            return result;
        }

        private boolean backtrace(List<List<String>> list, int queenCount, int row, char[][] chess) {
            if (queenCount == 0) {
                List<String> solution = covertSolution(chess);
                list.add(solution); 
            } else {
                if(row < chess.length){
                    for (int j = 0; j < chess.length; j++) {
                        if (isValid(chess, row, j)) {
                            chess[row][j] = 'Q';
                            if (backtrace(list, queenCount - 1, row + 1, chess)) {
                                return true;
                            } else {
                                chess[row][j] = '.';
                            }
                        }
                    }
                } 
            }
            return false;
        }

        private boolean isValid(char[][] chess, int row, int colum) {
            //column
            for (int i = 0; i < row; i++) {
                if (chess[i][colum] == 'Q') {
                    return false;
                }
            }

            //diagonal
            for (int i = row - 1; i >= 0; i--) {
                int i1 = colum - (row - i);
                if (i1 >= 0 && chess[i][i1] == 'Q') {
                    return false;
                }
                int i2 = colum + row - i;
                if (i2 < chess.length && chess[i][i2] == 'Q') {
                    return false;
                }
            }

            return true;
        }

        private List<String> covertSolution(char[][] chess) {
            List<String> result = new ArrayList<>();
            for (int i = 0; i < chess.length; i++) {
                StringBuilder sb = new StringBuilder();
                for (int j = 0; j < chess.length; j++) {
                    sb.append(chess[i][j]);
                }
                result.add(sb.toString());
            }
            return result;
        }
}
```

