# 513. Find Bottom Left Tree Value (2021.03.10)

https://leetcode.com/problems/find-bottom-left-tree-value/

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
- 从左至右层次遍历，保存每层的最左结点。

```java
class Solution {
    public int findBottomLeftValue(TreeNode root) {
        if (root == null) return 0;
        
        Queue<TreeNode> queue = new LinkedList<>();
        TreeNode leftmost = null;
        queue.offer(root);
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                TreeNode cur = queue.poll();
                if (i == 0) leftmost = cur;
                if (cur.left != null) queue.offer(cur.left);
                if (cur.right != null) queue.offer(cur.right);
            }
        }
        return leftmost.val;
    }
}
```

## Solution 2: BFS - right to left

- $TC:O(n)$
- $SC:O(n)$
- 从右到左进行BFS，那么最后一个结点必然是最底层的最左结点，不需要进行层次的判断。

```java
class Solution {
    public int findBottomLeftValue(TreeNode root) {
        if (root == null) return 0;
        
        Queue<TreeNode> queue = new LinkedList<>();
        TreeNode leftmost = null;
        queue.offer(root);
        while (!queue.isEmpty()) {
            TreeNode cur = queue.poll();
            leftmost = cur;
            if (cur.right != null) queue.offer(cur.right);
            if (cur.left != null) queue.offer(cur.left);
        }
        return leftmost.val;
    }
}
```

## Solution 3: DFS

- $TC:O(n)$
- $SC:O(h)$
- 使用包含两个值的res来保存返回的结果，其中res[0]表示某一层最左侧的结点值，res[1]表示该结点的深度。
- 进行DFS的同时追踪当前node对应的深度，如果深度大于res[1]，说明node是当前状态下最深层的最左侧结点，更新res。
- 否则，res仍然是当前遍历过的结点中所期望的答案，不对res进行更新并且返回res[0]。

```java
class Solution {
    public int findBottomLeftValue(TreeNode root) {
        return findBottomLeftValue(root, 1, new int[]{0, 0});
    }
    
    private int findBottomLeftValue(TreeNode root, int depth, int[] res) {
        if (root == null) return res[0];
        findBottomLeftValue(root.left, depth + 1, res);
        findBottomLeftValue(root.right, depth + 1, res);
        
        if (res[1] < depth) {
            res[0] = root.val;
            res[1] = depth;
        }
        return res[0];
    }
}
```