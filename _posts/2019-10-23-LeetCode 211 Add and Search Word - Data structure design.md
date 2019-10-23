## LeetCode: [Add and Search Word - Data structure design](https://leetcode.com/problems/add-and-search-word-data-structure-design/)

### 题目:

> 设计一个数据结构实现下面两个操作：
>
> ```java
> void addWord(word)
> bool search(word)
> ```
>
> **search(word)**方法可以查找出添加的单词，并且单词中的（'.'）字符代表任意存在的字母。

### 例子：

> ```java
> addWord("bad")
> addWord("dad")
>   addWord("mad")
>   search("pad") -> false
>   search("bad") -> true
>   search(".ad") -> true
> search("b..") -> true
> ```

### 思路:

> 这个题是一个设计数据结构的题，和trieNode那个题是类似的，需要一个这样的结构来根据字母树来查找单词。但是这里多了一个任意字符'.'，刚开始我改变了存储结构，按层来存储，这样看来就像一个单链表，但是这样其实是有问题的，会把不存在的单词也能查出来。正确的应该是插入还是相同的，查找通过递归的方式进行查找。

```java
 class WordDictionary {
        TrieNode root;

        /**
         * Initialize your data structure here.
         */
        public WordDictionary() {
            root = new TrieNode();
        }

        /**
         * Adds a word into the data structure.
         */
        public void addWord(String word) {
            if (null == word) {
                return;
            }
            char[] chars = word.toCharArray();
            TrieNode p = root;
            for (char aChar : chars) {
                int index = aChar - 'a';
                if (p.next[index] == null) {
                    p.next[index] = new TrieNode();
                }
                p = p.next[index];
            }
            p.isWord = true;
        }

        /**
         * Returns if the word is in the data structure. A word could contain the dot character '.' to represent any one letter.
         */
        public boolean search(String word) {
            if (null == word) {
                return false;
            }
            return matches(word.toCharArray(), root, 0);
        }

        private boolean matches(char[] chars, TrieNode root, int offset) {
            if (chars.length == offset) {
                return root.isWord;
            }
            char curChar = chars[offset];
            if ('.' == curChar) {
                for (int i = 0; i < root.next.length; i++) {
                    TrieNode trieNode = root.next[i];
                    if (null != trieNode && matches(chars, root.next[i], offset + 1)) {
                        return true;
                    }
                }
            } else {

                return root.next[chars[offset] - 'a'] != null && matches(chars, root.next[chars[offset] - 'a'], offset + 1);
            }
            return false;
        }
    }

     class TrieNode {
        boolean isWord;
        TrieNode[] next;

        public TrieNode() {
            next = new TrieNode[27];
            isWord = false;
        }
    }
```

