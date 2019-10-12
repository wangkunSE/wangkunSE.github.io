## LeetCode: [Maximum Width of Binary Tree](https://leetcode.com/problems/maximum-width-of-binary-tree/)

### 题目:

> 给定一个二叉树，求这个树的最大宽度。该宽度为两个非空节点间包含空节点的总结点数。

### 例子：

> ```java
> Example 1:
> 
> Input: 
> 
>            1
>          /   \
>         3     2
>        / \     \  
>       5   3     9 
> 
> Output: 4
> Explanation: 最大宽度是第三层，总共有四个节点 4 (5,3,null,9).
> Example 2:
> 
> Input: 
> 
>           1
>          /  
>         3    
>        / \       
>       5   3     
> 
> Output: 2
> Explanation:最大宽度是第三层 总共有两个节点 2 (5,3).
> ```

### 思路:

> 这个题也是最直观的感受就是在考察树的广度遍历，但是因为没有深入思考过，所以在怎么区分每一层上陷入了卡克状态。看了提示发现可以通过队列的大小来确定当前层有多少个节点。知道这个后这个题的核心问题是怎么算两个节点间的距离，其实每棵满二叉树都可以写成一个数组的形式，所以这里其实就需要记录两个值，一个是每一层最左边节点的索引，另一个是每一层最右边节点索引，通过这两个节点索引的差值就能够计算出当前层的最大宽度。

### 代码：

**DFS**

```java
class Solution {
 public int widthOfBinaryTree(TreeNode root) {
            if (root == null) {
                return 0;
            }
            List<Integer> start = new LinkedList<>();
            List<Integer> end = new LinkedList<>();

            return findMaxWidthOfBinaryTree(root, 0, 1, start, end);
        }

        private int findMaxWidthOfBinaryTree(TreeNode root, int level, int order, List<Integer> start, List<Integer> end) {
            if (root == null) {
                return 0;
            }
            if (start.size() == level) {
                start.add(order);
                end.add(order);
            } else {
                end.set(level, order);
            }
            int cur = end.get(level) - start.get(level) + 1;
            int left = findMaxWidthOfBinaryTree(root.left, level + 1, 2 * order, start, end);
            int right = findMaxWidthOfBinaryTree(root.right, level + 1, 2 * order + 1, start, end);

            return Math.max(cur, Math.max(left, right));
        }
}
```



>同样的BFS也可以实现

```java
class Solution {
private static final Map<TreeNode, Integer> nodeOrderMap = new HashMap<>();

        public int widthOfBinaryTree(TreeNode root) {
            if (root == null) {
                return 0;
            }
            LinkedList<TreeNode> queue = new LinkedList<>();
            nodeOrderMap.put(root, 1);
            queue.offer(root);
            int maxWidth = 0;
            while (!queue.isEmpty()) {
                int size = queue.size();
                maxWidth = Math.max(nodeOrderMap.get(queue.peekLast()) - nodeOrderMap.get(queue.peekFirst()) + 1, maxWidth);
                for (int i = 0; i < size; i++) {
                    TreeNode tempNode = queue.poll();
                    if (tempNode.left != null) {
                        queue.offer(tempNode.left);
                        nodeOrderMap.put(tempNode.left, nodeOrderMap.get(tempNode) * 2);
                    }

                    if (tempNode.right != null) {
                        queue.offer(tempNode.right);
                        nodeOrderMap.put(tempNode.right, nodeOrderMap.get(tempNode) * 2 + 1);
                    }

                }


            }

            return maxWidth;
        }
    }
}
```

