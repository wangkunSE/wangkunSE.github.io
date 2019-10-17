## LeetCode: [Gray Code](https://leetcode.com/problems/gray-code/)

### 题目:

> 灰码的含义是两个连续的数字在二进制上只有一位不同。给一个正整数n，返回一个灰码集合，该集合必须以0开始。

### 例子：

> ```java
> Input: 2
> Output: [0,1,3,2]
> Explanation:
> 00 - 0
> 01 - 1
> 11 - 3
> 10 - 2
> 
> 对于一个整数n，灰码的序列可能不是唯一的，如下.
> For example, [0,2,3,1] 也是一个合格的返回值.
> 
> 00 - 0
> 10 - 2
> 11 - 3
> 01 - 1
> ```

### 思路:

> 这个题是在刷dfs系列题的时候遇到的，刚开始以为很简单，不就是每一位选1还是0的问题吗，但是事实不是这样。最后发现规律，每增加一位实际是增加了当前位和已有集合的倒序列的和。
>
> n = 1 .    0		1
>
> n = 2 .    00   	01	|	11		10
>
> n = 3 .	000	001		011		010	|	110		111		101		100
>
> ...

```java
class Solution {
  public List<Integer> grayCode(int n) {
        List<Integer> rs = new ArrayList<Integer>();
        rs.add(0);
        for (int i = 0; i < n; i++) {
            int size = rs.size();
            for (int k = size - 1; k >= 0; k--) {
                int moveLeft = 1 << i;
                int curNum = rs.get(k) | moveLeft;
                rs.add(curNum);
            }
        }
        return rs;
    };
}
```

