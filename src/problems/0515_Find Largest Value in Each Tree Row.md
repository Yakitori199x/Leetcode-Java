# 515. Find Largest Value in Each Tree Row (2021.03.09)

https://leetcode.com/problems/find-largest-value-in-each-tree-row/

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

- $TC:O(n)$
- $SC:O(n)$

```java
class Solution {
    public List<Integer> largestValues(TreeNode root) {
        List<Integer> res = new LinkedList<>();
        if (root == null) return res;
        
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        while (!queue.isEmpty()) {
            int size = queue.size();
            int curMax = Integer.MIN_VALUE;
            for (int i = 0; i < size; i++) {
                TreeNode cur = queue.poll();
                curMax = Math.max(cur.val, curMax);
                if (cur.left != null) queue.offer(cur.left);
                if (cur.right != null) queue.offer(cur.right);
            }
            res.add(curMax);
        }
        return res;
    }
}
```

## Solution 2: DFS

- $TC:O(n)$
- $SC:O(h)$

```java
class Solution {
    public List<Integer> largestValues(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        dfs(root, 0, res);
        return res;
    }
    
    private void dfs(TreeNode root, int level, List<Integer> res) {
        if (root == null) return;
        
        if (level == res.size()) res.add(root.val);
        else res.set(level, Math.max(res.get(level), root.val));
        dfs(root.left, level + 1, res);
        dfs(root.right, level + 1, res);
    }
}
```