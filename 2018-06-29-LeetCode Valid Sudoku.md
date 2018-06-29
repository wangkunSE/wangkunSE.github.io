## LeetCode : Valid Sudoku

#### 题目:

> 验证一个未填满的9x9的数独盘是否遵守如下法则：
>
> 1. 每一行只出现一次1-9的数字
> 2. 每一列只出现一次1-9的数字
> 3. 每一个3x3的小方格只出现一次1-9的数字

#### 例子：

 >

#### 思路：

> 很简单，就是分别进行行，列，方格的字符校验即可。

#### 代码：

```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        int length = board.length;
        Map<Character,Integer> rowTemp = new HashMap<>(length);
        Map<Character,Integer> columTemp = new HashMap<>(length);
        //row colum
        for(int i = 0; i<length;i++ ){
            for(int j = 0; j<length;j++){
                //row
                if (checkBoardValid(board, rowTemp, i, j)) return false;
                //colum
                if (checkBoardValid(board, columTemp, j, i)) return false;

            }
            columTemp.clear();
            rowTemp.clear();
        }
        //boxes
        for(int i= 0; i < length; i+=3){
            for(int j = 0; j < length; j+=3){
                boolean flag = checkBoxes(board,i,j);
                if(!flag){
                    return false;
                }
            }
        }

        return true;
    }

    private boolean checkBoardValid(char[][] board, Map<Character, Integer> tempMap, int i, int j) {
        if(board[i][j] == '.' ){
            tempMap.put('.',0);
        }else if(board[i][j] != '.'){
            if(tempMap.get(board[i][j])!=null){
                return true;
            }else{
                tempMap.put(board[i][j],1);
            }
        }
        return false;
    }

    private boolean checkBoxes(char[][] board,int startI,int startJ){
        Map<Character,Integer> map = new HashMap<>();
        for(int i = startI; i< startI+3; i++){
            for(int j = startJ; j < startJ+3; j++){
                if (checkBoardValid(board, map, i, j)) return false;
            }
        }
        map.clear();
        return true;
    }
}
```

#### 有趣的解法：

```java
public boolean isValidSudoku(char[][] board) {
    Set seen = new HashSet();
    for (int i=0; i<9; ++i) {
        for (int j=0; j<9; ++j) {
            char number = board[i][j];
            if (number != '.')
                if (!seen.add(number + " in row " + i) ||
                    !seen.add(number + " in column " + j) ||
                    !seen.add(number + " in block " + i/3 + "-" + j/3))
                    return false;
        }
    }
    return true;
}
```



> 非常简短可读性也非常高，优秀！