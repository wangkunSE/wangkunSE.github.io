## LeetCode: [Restore IP Addresses](https://leetcode.com/problems/restore-ip-addresses/)

### 题目:

> 给定一个由数字组成的字符串，求出改数字组成的串可以转化的所有合法ip集合。

### 例子：

> ```java
> Input: "25525511135"
> Output: ["255.255.11.135", "255.255.111.35"]
> ```

### 思路:

> 首先合法的ip由四个数字组成并由3个（**.**） 进行分割，并且每个数字都大于0小于255。这个题也是一个典型的递归求解的问题，点的总数是确定的，只有3个，那么问题就是在哪里打点。然后再看其实第一次打点的位置只有3个，因为在第四个位置打点时就一定会大于255。所以问题就转换成找出第一个点的位置，然后再找子串的第一个点的位置，打够三个点结束。需要注意的是当串以0开始时，只能截取0。

```java
class Solution {
  public List<String> restoreIpAddresses(String s) {
        if (null == s || s.length() < 4) {
            return new ArrayList<>();
        }

        List<String> result = new ArrayList<>();
        List<String> ipSplit = new ArrayList<>();
        findAllIp(result, s, ipSplit);
        return result;
    }

    private void findAllIp(List<String> result, String s, List<String> ipSplit) {
        if ("".equals(s) && ipSplit.size() < 4 || ipSplit.size() > 4) {
            return;
        }
        if ("".equals(s)) {
            ipSplit.size();
            StringBuilder sb = new StringBuilder();
            for (int i = 0; i < ipSplit.size(); i++) {
                sb.append(ipSplit.get(i));
                if (i != 3) {
                    sb.append(".");
                }
            }
            result.add(sb.toString());
            return;
        }

        if (s.startsWith("0")) {
            ipSplit.add(s.substring(0, 1));
            findAllIp(result, s.substring(1), ipSplit);
            ipSplit.remove(ipSplit.size() - 1);
        } else {
            for (int i = 1; i < 4; i++) {
                if (i <= s.length()) {
                    String substring = s.substring(0, i);
                    int strInt = Integer.parseInt(substring);
                    if (strInt > 255 || strInt < 0) {
                        return;
                    }
                    ipSplit.add(substring);
                    findAllIp(result, s.substring(i), ipSplit);
                    ipSplit.remove(ipSplit.size() - 1);
                }
            }
        }
    }
}
```

