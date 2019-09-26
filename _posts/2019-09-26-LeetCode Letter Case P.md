## LeetCode: [Letter Case P](https://leetcode.com/problems/letter-case-permutation/)

### 题目:

> 给定一个字符串S，将字符串中的每个字母不包含数字分别变成大写和小写的方式，返回一个可能出现的字符串集合。

### 例子：

> ```java
> Examples:
> Input: S = "a1b2"
> Output: ["a1b2", "a1B2", "A1b2", "A1B2"]
> 
> Input: S = "3z4"
> Output: ["3z4", "3Z4"]
> 
> Input: S = "12345"
> Output: ["12345"]
> ```

### 思路:

> 这个题是在Subset那个题之后做的，做起来就很随便了，思路和那个一样，就是判断出是不是字母，如果是字母先把当前的字母变成小写走一遍，然后把当前字母变成大写走一遍。

### 代码：

```java
class Solution {
public List<String> letterCasePermutation(String source) {
        if (Objects.isNull(source) || "".equals(source)) {
            return new ArrayList<>();
        }

        char[] chars = source.toCharArray();
        List<String> result = new ArrayList<>();

        fillResult(0, result, chars, new char[chars.length]);
        return result;
    }

    private void fillResult(int offset, List<String> result, char[] chars, char[] target) {
        if (offset >= chars.length) {
            result.add(new String(target));
            return;
        }

        char aChar = chars[offset];
        if (('a' <= aChar && 'z' >= aChar)|| ('A' <= aChar && 'Z' >= aChar)) {
            target[offset] = Character.toLowerCase(aChar);
            fillResult(offset + 1, result, chars, target);
            target[offset] = Character.toUpperCase(aChar);
            fillResult(offset + 1, result, chars, target);
        }else {
            target[offset] = aChar;
            fillResult(offset + 1, result, chars, target);
        }

    }
}
```

