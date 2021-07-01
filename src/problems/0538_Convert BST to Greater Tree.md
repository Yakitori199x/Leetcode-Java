# 538. Convert BST to Greater Tree (2021.02.21)

https://leetcode.com/problems/convert-bst-to-greater-tree/

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
- 本题就是做一个中序遍历，记得保存比下一个待修改结点大的所有结点的值的和

```java
class Solution {
    private int prevSum = 0;
    
    public TreeNode convertBST(TreeNode root) {
        helper(root);
        return root;
    }
    
    private void helper(TreeNode root) {
        if (root == null) return ;
        
        helper(root.right);
        // 修改当前结点，且修改后结点的值就是下一个结点要加上的值
        root.val += prevSum;
        prevSum = root.val;
        helper(root.left);
    }
}
```