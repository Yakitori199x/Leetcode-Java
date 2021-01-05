## 110. Balanced Binary Tree (2020.08.31)

https://leetcode.com/problems/balanced-binary-tree/


```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
```

## Solution 1: Recursive

- $TC:O(NlogN)$
  1. 可以注意到这个方法其实进行了很多次重复计算，比如计算root的左子树和右子树高度是已经将树遍历过一次了，但是`isBalanced(root.left) && isBalanced(root.right)`又基于左右子树的根结点对树进行了一次遍历，这是大可不必的。
  2. 此外，对于该解法的时间复杂度，如果树为skewed tree的情况下，应该是$$O(N^2)$$。但是由于`Math.abs(leftHeight - rightHeight) <= 1 && ...`可以提前结束判断，即处理完根结点后即可结束。所以，这个算法的时间复杂度就是在树比较平衡时达到worst case，每层都要遍历O(N)个结点，遍历h次。
- $SC:O(h)$
- 根据题目的描述，Balanced Binary Tree就是每个结点的左右子树高度差不大于1
- 可以很直接地转换为递归，即先递归求得左子树和右子树的高度，检查它们的差是否不超过1
- 接下来对左子树和右子树的根结点进行同样的isBalanced判断

```java
class Solution {
    public boolean isBalanced(TreeNode root) {
        if (root == null) return true;
        
        int leftHeight = getHeight(root.left);
        int rightHeight = getHeight(root.right);
        
        return Math.abs(leftHeight - rightHeight) <= 1 && isBalanced(root.left) && isBalanced(root.right);
    }
    
    private int getHeight(TreeNode root) {
        if (root == null) return -1;
        return Math.max(getHeight(root.left), getHeight(root.right)) + 1;
    }
}
```

## Solution 2: Recursive (optimized)

- $TC:O(N)$
- $SC:O(h)$
- 基于Solution 1进行优化，不对每个结点重复求height，而是一路递归到leaf，然后一层一层往上返回height并判断是否满足要求。
- 若满足判断要求，则正常地返回height+1，否则，说明树已经不balanced，没有继续判断的必要，返回-1。

```java
class Solution {
    public boolean isBalanced(TreeNode root) {
        return getHeight(root) != -1;
    }
    
    private int getHeight(TreeNode root) {
        if (root == null) return 0;
        
        int leftHeight = getHeight(root.left);
        int rightHeight = getHeight(root.right);
        
        if (Math.abs(leftHeight - rightHeight) > 1 || leftHeight == - 1 || rightHeight == -1) {
            return -1;
        }
        
        return Math.max(leftHeight, rightHeight) + 1;
    }
}
```

## Solution 3: postorder traversal

- $TC:O(N)$
- $SC:O(h)$

```java
class Solution {
    public boolean isBalanced(TreeNode root) {
        if (root == null) return true;
        
        Deque<TreeNode> stack = new LinkedList<>();
        Map<TreeNode, Integer> map = new HashMap<>();
        TreeNode temp = root;
        TreeNode last = null;
        
        while (!stack.isEmpty() || temp != null) {
            if (temp != null) {
                // 将当前结点压栈，往左移动
                stack.push(temp);
                temp = temp.left;
            } else {
                // 当前结点为null，说明前一结点没有左子树
                // 或是刚处理完某一结点（计算左右子树的高度差）
                // 由于是后序遍历，在右子树没有处理完之前，根结点不能被pop
                temp = stack.peek();

                // 当右子树为空或是上一个处理完的结点是当前结点的右子树的根
                if (temp.right == null || temp.right == last) {
                    temp = stack.pop();
                    int leftHeight = map.getOrDefault(temp.left, 0);
                    int rightHeight = map.getOrDefault(temp.right, 0);
                    if (Math.abs(leftHeight - rightHeight) > 1) return false;
                    // 记录当前结点的高度
                    map.put(temp, Math.max(leftHeight, rightHeight) + 1);
                    last = temp;
                    temp = null;
                } else {
                    temp = temp.right;
                }
            }
        }
        
        return true;
    }
}
```

