## 100. Same Tree (2020.08.23)

https://leetcode.com/problems/same-tree/


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
- $SC:O(h)$, h is the height of the tree
- Check whether values of current roots are equal, then check those of left subtree & right subtree

```java
class Solution {
    public boolean isSameTree(TreeNode p, TreeNode q) {
        if (p == null && q == null) return true;
        if (p == null || q == null) return false;
        
        return p.val == q.val && isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
    }
}
```

## Solution 2: Stack (DFS)

- $TC:O(N)$
- $SC:O(h)$ , h is the height of the tree
- Use a stack to store the pair of nodes in the same position (2 pushes, 2 pops)
- Iterate the tree in `pre-order`

```java
class Solution {
    public boolean isSameTree(TreeNode p, TreeNode q) {
        Deque<TreeNode> stack = new LinkedList<>();
        stack.push(p);
        stack.push(q);
        
        while (!stack.isEmpty()) {
            TreeNode m = stack.pop(), n = stack.pop();
            if (m == null && n == null) continue;
            if (m == null || n == null || m.val != n.val) return false;
            
            stack.push(m.left);
            stack.push(n.left);
            stack.push(m.right);
            stack.push(n.right);
        }
        
        return true;
    }
}
```

## Solution 3: Queue (BFS)

- $TC:O(N)$
- $SC:O(N)$, 当二叉树为满树时，最后一层的node数为 `(N / 2 + 1)`
- Use a queue to store the pair of nodes in the same position (2 pushes, 2 pops)
- Iterate the tree in `level-order`

```java
class Solution {
    public boolean isSameTree(TreeNode p, TreeNode q) {
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(p);
        queue.add(q);
        
        while (!queue.isEmpty()) {
            TreeNode m = queue.remove(), n = queue.remove();
            if (m == null && n == null) continue;
            if (m == null || n == null || m.val != n.val) return false;
            
            queue.add(m.left);
            queue.add(n.left);
            queue.add(m.right);
            queue.add(n.right);
        }
        
        return true;
    }
}
```

