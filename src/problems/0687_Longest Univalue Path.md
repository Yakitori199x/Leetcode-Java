# 687. Longest Univalue Path (2021.02.20)

https://leetcode.com/problems/longest-univalue-path/

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

## Solution 1: DFS (with parent node)

- $TC:O(N)$
- $SC:O(h)$
- 本题所求的path和一般意义上树的path不太一样，它是可以经过某个root弯曲的。
- 所以，本题的关键在于要求的答案是包含了以某个root为中心的左右子树的符合条件的path。
- 而需要返回给parent（caller）的只能是左右子树中较大的那个path。

```java
class Solution {
    private int res = 0;
    
    public int longestUnivaluePath(TreeNode root) {
        longestUnivaluePath(root, null);
        return res;
    }
    
    private int longestUnivaluePath(TreeNode root, TreeNode parent) {
        if (root == null) return 0;
        
        int left = longestUnivaluePath(root.left, root);
        int right = longestUnivaluePath(root.right, root);
        res = Math.max(res, left + right);
        
        // parent非空且当前root的值和parent的值相等时才满足条件
        if (parent != null && root.val == parent.val) {
            // 返回左子树和右子树中满足条件的单向path的较大者
            // 返回的是以root为起点的path上的结点数，对于parent来说就是path的长度
            return Math.max(left, right) + 1;
        }
        
        return 0;
    }
}
```

## Solution 2: DFS (no parent node)

- $TC:O(N)$
- $SC:O(h)$

```java
class Solution {
    private int res = 0;
    
    public int longestUnivaluePath(TreeNode root) {
        helper(root);
        return res;
    }
    
    private int helper(TreeNode root) {
        if (root == null) return 0;
        
        int left = helper(root.left);
        int right = helper(root.right);
        
        if (root.left != null && root.left.val == root.val) left += 1;
        else left = 0;
        
        if (root.right != null && root.right.val == root.val) right += 1;
        else right = 0;
        
        res = Math.max(res, left + right);
        return Math.max(left, right);
    }
}
```