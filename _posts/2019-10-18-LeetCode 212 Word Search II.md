## LeetCode: [Word Search II](https://leetcode.com/problems/word-search-ii/)

### 题目:

> 给定一个二维字符数组和一个单词集合，找出所有在数组中的单词集合。每一个单词的字符必须在相邻的单元格中。

### 例子：

> ```java
> Input: 
> board = [
>   ['o','a','a','n'],
>   ['e','t','a','e'],
>   ['i','h','k','r'],
>   ['i','f','l','v']
> ]
> words = ["oath","pea","eat","rain"]
> 
> Output: ["eat","oath"]
> ```

### 思路:

> 这个题本身已经不陌生了，一看就是要使用dfs的解法。但是肯定没有这么简单，因为这是一道hard的题，所以肯定对时间复杂度是有要求的，一个个单词的找肯定会超时，想来想去没有想到很好的办法，最后看了看别人的做法，了解到TireNode数据结构可以大大缩减查找的时间。具体结构与解法如下。

```java
class Solution {
   public List<String> findWords(char[][] board, String[] words) {

        if (null == board || null == words) {
            return new ArrayList<>();
        }

        TireNode root = buildTireNodes(words);
        List<String> result = new ArrayList<>();
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                findAllResults(result, i, j, root, board);
            }
        }

        return result;
    }

    private void findAllResults(List<String> result, int row, int column, TireNode root, char[][] board) {
        char curChar = board[row][column];
        if ('#' == curChar || root.next[curChar - 'a'] == null) {
            return;
        }
        TireNode p = root.next[curChar - 'a'];
        if (null != p.word) {
            result.add(p.word);
            p.word = null;
        }

        board[row][column] = '#';
        if (row > 0) {
            findAllResults(result, row - 1, column, p, board);
        }

        if (column > 0) {
            findAllResults(result, row, column - 1, p, board);
        }

        if (row < board.length - 1) {
            findAllResults(result, row + 1, column, p, board);
        }

        if (column < board[0].length - 1) {
            findAllResults(result, row, column + 1, p, board);
        }

        board[row][column] = curChar;
    }

    private TireNode buildTireNodes(String[] words) {
        TireNode root = new TireNode();
        for (String word : words) {
            TireNode p = root;
            for (char curChar : word.toCharArray()) {
                int index = curChar - 'a';
                if (p.next[index] == null) {
                    p.next[index] = new TireNode();
                }
                p = p.next[index];
            }
            p.word = word;
        }

        return root;
    }


    static class TireNode {
        public TireNode[] next = new TireNode[26];
        public String word;
    }
}
```

