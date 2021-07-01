# 617. Merge Two Binary Trees (2021.02.28)

https://leetcode.com/problems/merge-two-binary-trees/

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

## Solution 1: DFS, create new nodes

- $TC:O(N)$，N为t1和t2的所有结点数（重合部分只算一次）
- $SC:O(N)$
- 同时遍历t1和t2并构造新的结点，由于存在t1不为null而t2为null，或是t1为null而t2不为null的情况。
- 我们在计算val以及对左右子树递归的时候都要先判断一下。

```java
class Solution {
    public TreeNode mergeTrees(TreeNode root1, TreeNode root2) {
        if (root1 == null && root2 == null) return null;
        
        int val = (root1 == null ? 0 : root1.val) + (root2 == null ? 0 : root2.val);
        TreeNode node = new TreeNode(val);
        
        // 判断一下再赋值，可以有效防止NPE
        TreeNode left1 = (root1 == null) ? null : root1.left, right1 = (root1 == null) ? null : root1.right;
        TreeNode left2 = (root2 == null) ? null : root2.left, right2 = (root2 == null) ? null : root2.right;
        node.left = mergeTrees(left1, left2);
        node.right = mergeTrees(right1, right2);
        return node;
    }
}
```

## Solution 2: DFS, based on existing nodes

- $TC:O(N)$
- $SC:O(N)$
- t1和t2重合的结点重新创建，未重合部分则直接返回t1或是t2对应部分的root。

```java
class Solution {
    public TreeNode mergeTrees(TreeNode root1, TreeNode root2) {
        if (root1 == null || root2 == null) {
            return (root1 == null) ? root2 : root1;
        }
        
        TreeNode node = new TreeNode(root1.val + root2.val);
        node.left = mergeTrees(root1.left, root2.left);
        node.right = mergeTrees(root1.right, root2.right);
        return node;
    }
}
```