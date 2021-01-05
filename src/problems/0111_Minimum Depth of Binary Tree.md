## 111. Minimum Depth of Binary Tree (2020.09.01)

https://leetcode.com/problems/minimum-depth-of-binary-tree/


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

## Solution 1: BFS

- $TC:O(N)$
- $SC:O(N)$

```java
class Solution {
    public int minDepth(TreeNode root) {
        if (root == null) return 0;
        
        Queue<TreeNode> queue = new LinkedList<>();
        int res = 1;
        
        queue.offer(root);
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                TreeNode temp = queue.poll();
                // 遇到叶子结点，直接返回所在的level
                if (temp.left == null && temp.right == null) return res;
                
                if (temp.left != null) queue.offer(temp.left);
                if (temp.right != null) queue.offer(temp.right);
            }
            res++;
        }
        
        return res;
    }
}
```

## Solution 2: DFS (Recursive)

- $TC:O(N)$
- $SC:O(h)$

```java
class Solution {
    public int minDepth(TreeNode root) {
        if (root == null) return 0;
        
        int left = minDepth(root.left);
        int right = minDepth(root.right);
        
        // 当只有一边子树不存在时，不能取两边depth的最小值，而是要取最大值
        // 当左右子树均存在时，取左右子树depth的最小者
        return (left == 0 || right == 0) ? Math.max(left, right) + 1 : Math.min(left, right) + 1;
    }
}
```

## Solution 3: DFS (Iterative)

- $TC:O(N)$
- $SC:O(h)$

```java
class Solution {
    public int minDepth(TreeNode root) {
        if (root == null) return 0;
        
        Deque<TreeNode> nodes = new LinkedList<>();
        Deque<Integer> depth = new LinkedList<>();
        int res = Integer.MAX_VALUE;
        
        nodes.push(root);
        depth.push(1);
        while (!nodes.isEmpty()) {
            TreeNode temp = nodes.pop();
            int tempDepth = depth.pop();
            // 遇到叶子结点不能直接返回当前的深度
            if (temp.left == null && temp.right == null) res = Math.min(res, tempDepth);
            
            if (temp.right != null) {
                nodes.push(temp.right);
                depth.push(tempDepth + 1);
            }
            
            if (temp.left != null) {
                nodes.push(temp.left);
                depth.push(tempDepth + 1);
            }
        }
        
        return res;
    }
}
```

