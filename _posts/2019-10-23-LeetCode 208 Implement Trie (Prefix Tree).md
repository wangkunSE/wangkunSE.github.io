## LeetCode: [Implement Trie (Prefix Tree)](https://leetcode.com/problems/implement-trie-prefix-tree/)

### 题目:

> 实现一个tire的数据结构实现下面的操作：
>
> ```java
>  public void insert(String word) ;
>  public boolean search(String word) ;
>  public boolean startsWith(String prefix) ;
> ```
>
> **search(word)**方法可以查找出添加的单词，startsWith方法可以查找有没有以prefix为前缀的单词。

### 例子：

> ```java
> Trie trie = new Trie();
> 
> trie.insert("apple");
> trie.search("apple");   // returns true
> trie.search("app");     // returns false
> trie.startsWith("app"); // returns true
> trie.insert("app");   
> trie.search("app");     // returns true
> ```

### 思路:

> 这个题是一个设计数据结构的题，关键点在于怎么能够查找单词前缀。最好的方法就是将字母分存，然后一层一层的去构建单词。实现如下。

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

