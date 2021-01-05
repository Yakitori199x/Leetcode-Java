## 104. Maximum Depth of Binary Tree (2020.08.22)

https://leetcode.com/problems/maximum-depth-of-binary-tree/


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

- $TC:O(N)$
- $SC:O(h)$

```java
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) return 0;
        return Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
    }
}
```

## Solution 2: DFS (Iterative)

- $TC:O(N)$
- $SC:O(h)$

```java
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) return 0;
        
        Deque<TreeNode> nodeStack = new LinkedList<>();
        Deque<Integer> depthStack = new LinkedList<>();
        int res = 0;
        
        nodeStack.push(root);
        depthStack.push(1);
        
        while (!nodeStack.isEmpty()) {
            TreeNode tempNode = nodeStack.pop();
            int tempDepth = depthStack.pop();
            
            if (tempNode.left == null && tempNode.right == null) res = Math.max(res, tempDepth);
            
            if (tempNode.right != null) {
                nodeStack.push(tempNode.right);
                depthStack.push(tempDepth + 1);
            }
            
            if (tempNode.left != null) {
                nodeStack.push(tempNode.left);
                depthStack.push(tempDepth + 1);
            }
        }
        
        return res;
    }
}
```

## Solution 3: BFS

- $TC:O(N)$
- $SC:O(N)$, when the tree is a full binary tree

```java
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) return 0;
        
        Queue<TreeNode> queue = new LinkedList<>();
        int res = 0;
        queue.offer(root);
        
        while (!queue.isEmpty()) {
            res++;
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                TreeNode temp = queue.poll();
                if (temp.left != null) queue.offer(temp.left);
                if (temp.right != null) queue.offer(temp.right);
            }
        }
        
        return res;
    }
}
```

