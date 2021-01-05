## 107. Binary Tree Level Order Traversal II (2020.10.17)

https://leetcode.com/problems/binary-tree-level-order-traversal-ii/


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
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        List<List<Integer>> res = new LinkedList<>();
        if (root == null) return res;
        
        Queue<TreeNode> nodes = new LinkedList<>();
        
        nodes.offer(root);
        while (!nodes.isEmpty()) {
            int size = nodes.size();
            List<Integer> curLevel = new LinkedList<>();
            for (int i = 0; i < size; i++) {
                TreeNode curNode = nodes.poll();
                curLevel.add(curNode.val);
                if (curNode.left != null) nodes.offer(curNode.left);
                if (curNode.right != null) nodes.offer(curNode.right);
            }

            // remember to use LinkedList for res because of insertion in front
            res.add(0, curLevel);
        }
        
        return res;
    }
}
```

## Solution 2: DFS

- $TC:O(N)$
- $SC:O(h)$
- 添加一个level变量来确认是否要创建对应该层的新的list，以及来定位当前value应该加入到哪个list

```java
class Solution {
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        List<List<Integer>> res = new LinkedList<>();
        dfs(root, res, 0);
        return res;
    }
    
    private void dfs(TreeNode root, List<List<Integer>> res, int level) {
        if (root == null) return ;
        
        if (res.size() <= level) res.add(0, new ArrayList<>());
        res.get(res.size() - level - 1).add(root.val);
        dfs(root.left, res, level + 1);
        dfs(root.right, res, level + 1);
    }
}
```

