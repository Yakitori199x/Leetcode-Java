# 124. Binary Tree Maximum Path Sum (2021.02.21)

https://leetcode.com/problems/binary-tree-maximum-path-sum/

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
- 本题的难点在于对负值的处理。

```java
class Solution {
    // 初始值设置为最小负值，应对诸如全结点均为负值的情况
    private int maxSum = Integer.MIN_VALUE;
    
    public int maxPathSum(TreeNode root) {
        helper(root);
        return maxSum;
    }
    
    private int helper(TreeNode root) {
        if (root == null) return 0;
        
        // 如果从左子树或右子树返回的是负值，则直接设置为0，否则会越加越小
        int leftSum = Math.max(0, helper(root.left));
        int rightSum = Math.max(0, helper(root.right));
        
        maxSum = Math.max(maxSum, leftSum + rightSum + root.val);
        
        return Math.max(leftSum, rightSum) + root.val;
    }
}
```

