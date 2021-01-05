## 199. Binary Tree Right Side View (2020.10.12)

https://leetcode.com/problems/binary-tree-right-side-view/


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
- 取每一层的最右结点 => i = size - 1时的结点

```java
class Solution {
    public List<Integer> rightSideView(TreeNode root) {
        Queue<TreeNode> queue = new LinkedList<>();
        List<Integer> res = new LinkedList<>();
        
        if (root == null) return res;
        
        queue.offer(root);
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                TreeNode tmp = queue.poll();
                
                if (tmp.left != null) queue.offer(tmp.left);
                if (tmp.right != null) queue.offer(tmp.right);
                if (i == size - 1) res.add(tmp.val);
            }
        }
        
        return res;
    }
}
```

## Solution 2: DFS

- $TC:O(N)$
- $SC:O(h)$
- 用当前深度来判断是否加入结点的值 => 如果当前深度等于res的size，则说明当前结点是该层的首结点（最左或最右）

```java
class Solution {
    public List<Integer> rightSideView(TreeNode root) {
        List<Integer> res = new LinkedList<>();
        rightSideView(root, res, 0);
        return res;
    }
    
    private void rightSideView(TreeNode root, List<Integer> res, int depth) {
        if (root == null) return;
        
        if (res.size() == depth) res.add(root.val);
        
        rightSideView(root.right, res, depth + 1);
        rightSideView(root.left, res, depth + 1);
    }
}
```

