## 101. Symmetric Tree (2020.08.23)

https://leetcode.com/problems/symmetric-tree/


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

## Solution 1: Recursion (DFS)

- $TC:O(N)$
- $SC:O(h)$ , h is the height of the tree, O(N) in worst case
- 要判断一棵树是否symmetric，首先要判断当前结点 `p` 和 `q` 的value是否相等，然后判断 `p` 的左子树和 `q` 的右子树是否symmetric，以及 `p` 的右子树和 `q` 的左子树是否symmetric `（p.s. 根结点独立处理）`

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if (root == null) return true;
        return isSymmetric(root.left, root.right);
    }
    
    private boolean isSymmetric(TreeNode p, TreeNode q) {
        if (p == null && q == null) return true;
        if (p == null || q == null) return false;
        
        return p.val == q.val && isSymmetric(p.left, q.right) && isSymmetric(p.right, q.left);
    }
}
```

## Solution 2: Stack (DFS)

- $TC:O(N)$
- $SC:O(h)$, h is the height of the tree, O(N) in worst case
- Iterative method, the processing procedure is the same as the recursive one

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if (root == null) return true;
        
        Deque<TreeNode> stack = new LinkedList<>();
        stack.push(root.left);
        stack.push(root.right);
        
        while (!stack.isEmpty()) {
            TreeNode p = stack.pop(), q = stack.pop();
            if (p == null && q == null) continue;
            if (p == null || q == null || p.val != q.val) return false;
            
            stack.push(p.right);
            stack.push(q.left);
            stack.push(p.left);
            stack.push(q.right);
        }
        
        return true;
    }
}
```

## Solution 3: Queue (BFS)

- $TC:O(N)$
- $SC:O(N)$, 当二叉树为满树时，最后一层的node数为 `(N / 2 + 1)`

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if (root == null) return true;
        
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root.left);
        queue.add(root.right);
        
        while (!queue.isEmpty()) {
            TreeNode p = queue.poll(), q = queue.poll();
            if (p == null && q == null) continue;
            if (p == null || q == null || p.val != q.val) return false;
            
            queue.add(p.left);
            queue.add(q.right);
            queue.add(p.right);
            queue.add(q.left);
        }
        
        return true;
    }
}
```

