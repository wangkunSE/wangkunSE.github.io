## LeetCode: [Generate Parentheses](https://leetcode.com/problems/generate-parentheses/)

### 题目:

> 给定一个数字n，写一个函数返回所有n对小括号的组合。

### 例子：

> ```java
> For example, given n = 3, a solution set is:
> 
> [
>   "((()))",
>   "(()())",
>   "(())()",
>   "()(())",
>   "()()()"
> ]
> ```

### 思路:

> 这个题也是明显的回溯的题，就是看当前选择哪个括号，但是需要注意的是左括号数一定要小于等于右括号数，当两种括号都使用完了就是一种解。

### 代码：

```java
class Solution {
 public List<String> generateParenthesis(int n) {

        if (n <= 0) {
            return new ArrayList<>();
        }

        List<String> result = new ArrayList<>();
        int charLength = 2 * n;
        fillParentheseResult(result, n, n, new char[charLength], 0);
        return result;
    }

    private void fillParentheseResult(List<String> result, int left, int right, char[] temp, int offset) {
        if (right == 0) {
            result.add(new String(temp));
            return;
        }

        if (left == right && left > 0) {
            
            temp[offset] = '(';
            fillParentheseResult(result, left - 1, right, temp, offset + 1);
        } else if (left > 0 && right > 0 && left < right) {

            temp[offset] = '(';
            fillParentheseResult(result, left - 1, right, temp, offset + 1);

            temp[offset] = ')';
            fillParentheseResult(result, left, right - 1, temp, offset + 1);
        } else if (left == 0 && right > 0) {

            temp[offset] = ')';
            fillParentheseResult(result, left, right - 1, temp, offset + 1);
        }

    }
}
```



>会发现以上代码比较繁琐，因为条件判断。但是仔细想了下条件判断可以简化成如下样子

```java
class Solution {
 public List<String> generateParenthesis(int n) {

        if (n <= 0) {
            return new ArrayList<>();
        }

        List<String> result = new ArrayList<>();
        int charLength = 2 * n;
        fillParentheseResult(result, n, n, new char[charLength], 0);
        return result;
    }

    private void fillParentheseResult(List<String> result, int left, int right, char[] temp, int offset) {
        if (right == 0) {
            result.add(new String(temp));
            return;
        }

        if (left > 0) {

            temp[offset] = '(';
            fillParentheseResult(result, left - 1, right, temp, offset + 1);
        }

        if (left < right) {

            temp[offset] = ')';
            fillParentheseResult(result, left, right - 1, temp, offset + 1);
        }

    }
}
```

