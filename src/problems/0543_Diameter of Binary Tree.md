# 543. Diameter of Binary Tree (2021.02.21)

https://leetcode.com/problems/diameter-of-binary-tree/

## Related Questions:
- 687. Longest Univalue Path

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

## Solution 1: DFS

- $TC:O(N)$
- $SC:O(h)$
- 本题相对于LC687简化了对path上结点的值需要相等的要求。

```java
class Solution {
    private int diameter = 0;
    
    public int diameterOfBinaryTree(TreeNode root) {
        helper(root);
        return diameter;
    }
    
    private int helper(TreeNode root) {
        if (root == null) return 0;
        
        int left = helper(root.left);
        int right = helper(root.right);
        diameter = Math.max(diameter, left + right);
        return Math.max(left, right) + 1;
    }
}
```