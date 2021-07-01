# 1161. Maximum Level Sum of a Binary Tree (2021.03.11)

https://leetcode.com/problems/maximum-level-sum-of-a-binary-tree/

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

## Solution 1: BFS - level order traversal

- $TC:O(n)$
- $SC:O(n)$

```java
class Solution {
    public int maxLevelSum(TreeNode root) {
        if (root == null) return 0;
        
        Queue<TreeNode> queue = new LinkedList<>();
        int maxLevel = 0, curLevel = 1, maxSum = Integer.MIN_VALUE;
        queue.offer(root);
        while (!queue.isEmpty()) {
            int size = queue.size(), levelSum = 0;
            for (int i = 0; i < size; i++) {
                TreeNode cur = queue.poll();
                levelSum += cur.val;
                if (cur.left != null) queue.offer(cur.left);
                if (cur.right != null) queue.offer(cur.right);
            }
            
            if (levelSum > maxSum) {
                maxSum = levelSum;
                maxLevel = curLevel;
            }
            curLevel++;
        }
        return maxLevel;
    }
}
```

## Solution 2: DFS

- $TC:O(n)$
- $SC:O(h)$
- 用DFS实现层次遍历的效果（不是真正的层次遍历，但是可以通过depth将同一层的结点信息集合在一起）。

```java
class Solution {
    public int maxLevelSum(TreeNode root) {
        List<Integer> sums = new ArrayList<>();
        dfs(root, 0, sums);
        int maxLevel = 0;
        for (int i = 0; i < sums.size(); i++) {
            if (sums.get(i) > sums.get(maxLevel)) maxLevel = i; 
        }
        return maxLevel + 1;
    }
    
    private void dfs(TreeNode root, int level, List<Integer> sums) {
        if (root == null) return;
        
        if (sums.size() == level) sums.add(root.val);
        else sums.set(level, sums.get(level) + root.val);
        dfs(root.left, level + 1, sums);
        dfs(root.right, level + 1, sums);
    }
}
```