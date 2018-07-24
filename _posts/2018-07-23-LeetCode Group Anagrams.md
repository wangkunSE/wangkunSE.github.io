## LeetCode:[Group Anagrams](https://leetcode.com/problems/group-anagrams/)

#### 题目：

> 给一个数组的字符串，将其中含有相同字母的分组

#### 例子：

> ```java
> Input: ["eat", "tea", "tan", "ate", "nat", "bat"],
> Output:
> [
>   ["ate","eat","tea"],
>   ["nat","tan"],
>   ["bat"]
> ]
> ```

#### 思路:

> 这道题很简单，就是遍历数组，放进Map中，但是第一次我将字符的和作为Key，这里就出问题了，因为存在不同字符组合相同值得情况，比如ad和bc。后面改成用排过序的字符数组新生成的字符串作为Key就没问题了。但是这个做法复杂度为`O(nmlogm)`，有优化空间。

#### 代码：

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        List<List<String>> result = new ArrayList<>();
        if(strs == null || strs.length < 1){
            return result;
        }
        
        Map<String,List<String>> map = new HashMap<>();
        for(String str: strs){
            int temp = 0;
            char[] chars = str.toCharArray();
            Arrays.sort(chars);
            String key = new String(chars);
            if( map.containsKey(key)){
                map.get(key).add(str);
            }else{
                List<String> list = new ArrayList<>();
                list.add(str);
                map.put(key,list);
            }
        }
        
        result.addAll(map.values());
        return result;
    }
}
```

