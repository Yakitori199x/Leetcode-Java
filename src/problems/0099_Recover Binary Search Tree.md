# 99. Recover Binary Search Tree (2021.03.06)

https://leetcode.com/problems/recover-binary-search-tree/

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

## Solution 1: Inorder, naive

- $TC:O(n)$
- $SC:O(n)$
- 本题的目的就是找出BST上顺序被错误调换的两个结点。
- 因为BST的中序遍历的结果就是一个单调递增的序列，因此可以将中序遍历结果保存在一个数列中。
- 然后找出破坏了单调递增性质的两个结点。
- 最后交换它们的值即可。

```java
class Solution {
    public void recoverTree(TreeNode root) {
        List<TreeNode> nodes = new ArrayList<>();
        inorder(root, nodes);
        
        TreeNode first = null, second = null;
        for (int i = 0; i < nodes.size() - 1; i++) {
            if (nodes.get(i).val > nodes.get(i + 1).val) {
                // 需要注意可能是顺序上相邻的两个结点被交换了
                // 这种情况下nodes.get(i).val > nodes.get(i + 1).val只会进入一次
                // 所以second不需要判断直接赋值
                if (first == null) first = nodes.get(i);
                second = nodes.get(i + 1);
            }
        }
        
        int tempVal = first.val;
        first.val = second.val;
        second.val = tempVal;
    }
    
    private void inorder(TreeNode root, List<TreeNode> nodes) {
        if (root == null) return ;
        
        inorder(root.left, nodes);
        nodes.add(root);
        inorder(root.right, nodes);
    }
}
```

## Solution 2: Inorder, O(1)

- $TC:O(n)$
- $SC:O(h)$
- Solution1的改进版本，只需要O(h)的空间复杂度（worst case仍然是O(n)）。
- 关键在于一边进行中序遍历，一边根据prev和root的值的大小关系来确定是否是不满足单调递增顺序的结点。

```java
class Solution {
    private TreeNode first = null;
    private TreeNode second = null;
    private TreeNode prev = null;
    
    public void recoverTree(TreeNode root) {
        inorder(root);
        
        int tempVal = first.val;
        first.val = second.val;
        second.val = tempVal;
    }
    
    private void inorder(TreeNode root) {
        if (root == null) return ;
        
        inorder(root.left);
        if (prev != null && prev.val > root.val) {
            if (first == null) first = prev;
            second = root;
        }
        prev = root;
        inorder(root.right);
    }
}
```

## Solution 3: Morris traversal

- $TC:O(n)$
- $SC:O(1)$
- 应用了Morris Traversal的中序遍历，可以将空间复杂度优化到O(1)。
- 关于Morris Traversal可见：https://www.cnblogs.com/AnnieKim/archive/2013/06/15/morristraversal.html

```java
class Solution {
    private TreeNode first = null;
    private TreeNode second = null;
    private TreeNode prev = null;
    
    public void recoverTree(TreeNode root) {
        if (root == null) return ;
        
        TreeNode cur = root;
        while (cur != null) {
            if (cur.left == null) {
                if (prev != null && prev.val > cur.val) {
                    if (first == null) first = prev;
                    second = cur;
                }
                prev = cur;
                cur = cur.right;
            } else {
                TreeNode temp = cur.left;
                while (temp.right != null && temp.right != cur) temp = temp.right;
                
                if (temp.right == null) {
                    temp.right = cur;
                    cur = cur.left;
                } else {
                    if (prev != null && prev.val > cur.val) {
                        if (first == null) first = prev;
                        second = cur;
                    }
                    
                    temp.right = null;
                    prev = cur;
                    cur = cur.right;
                }
            }
        }
        
        int tempVal = first.val;
        first.val = second.val;
        second.val = tempVal;
    }
}
```

