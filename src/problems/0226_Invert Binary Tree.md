# 226. Invert Binary Tree (2021.02.11)

https://leetcode.com/problems/invert-binary-tree/

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

## Solution 1: DFS (Recursive)

- $TC:O(n)$
- $SC:O(h)$
- 只要能够遍历到树的每一个结点，并且交换左右子树即可。
- 即不管是通过前序，中序，后序，还是BFS都可以实现。

```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) return null;
        
        TreeNode left = invertTree(root.left);
        TreeNode right = invertTree(root.right);
        root.left = right;
        root.right = left;
        
        return root;
    }
}
```

## Solution 2: DFS (Iterative)

- $TC:O(n)$
- $SC:O(h)$

```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) return null;
        
        Deque<TreeNode> stack = new LinkedList<>();
        stack.push(root);
        
        while (!stack.isEmpty()) {
            TreeNode node = stack.pop();
            TreeNode temp = node.left;
            node.left = node.right;
            node.right = temp;
            
            if (node.left != null) stack.push(node.left);
            if (node.right != null) stack.push(node.right);
        }
        
        return root;
    }
}
```

## Solution 3: BFS

- $TC:O(n)$
- $SC:O(n)$, For a full binary tree, the leaf level has ⌈n/2⌉ = O(n) leaves.

```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) return null;
        
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        
        while (!queue.isEmpty()) {
            TreeNode node = queue.poll();
            TreeNode temp = node.left;
            node.left = node.right;
            node.right = temp;
                
            if (node.left != null) queue.offer(node.left);
            if (node.right != null) queue.offer(node.right);
        }
        
        return root;
    }
}
```