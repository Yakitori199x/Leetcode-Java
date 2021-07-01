# 993. Cousins in Binary Tree (2021.03.12)

https://leetcode.com/problems/cousins-in-binary-tree/

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
- 使用层次遍历免去了记录当前结点所处深度的麻烦。
- 只需要记录当前结点的parent，然后遍历过程中检查同一层符合x和y的结点的parent是否相同即可。
- 或是去检查当前结点的左右子树的根结点是否同时为x和y，并用两个Boolean来标识该层的遍历是否找到x和y（此处的实现）。

```java
class Solution {
    public boolean isCousins(TreeNode root, int x, int y) {
        if (root == null) return false;
        
        Queue<TreeNode> queue = new LinkedList<>();
        boolean isXFound = false, isYFound = false;
        queue.offer(root);
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                TreeNode cur = queue.poll();
                
                // 上层结点的检查确保了可以放心地把isXFound和isYFound设定为true
                // 即不会出现同一层的x和y属于同一个parent
                if (cur.val == x) isXFound = true;
                if (cur.val == y) isYFound = true;
                
                // 检查左右子树根结点是否同时为x和y
                if (cur.left != null && cur.right != null) {
                    if (cur.left.val == x && cur.right.val == y) return false;
                    if (cur.left.val == y && cur.right.val == x) return false;
                }
                
                if (cur.left != null) queue.offer(cur.left);
                if (cur.right != null) queue.offer(cur.right);
            }
            
            // 当该层遍历结束后，x和y都被找到则返回true
            // 若只有x或y中的某一个被找到，说明x和y不在同一层，返回false
            if (isXFound && isYFound) return true;
            else if (isXFound || isYFound) return false;
        }
        
        return true;
    }
}
```

## Solution 2: DFS

- $TC:O(n)$
- $SC:O(h)$
- 进行DFS并且用depthX，parentX以及depthY，parentY分别记录x结点和y结点的深度和父结点。
- 最后判断x和y的对应深度是不是相等的，且各自的父结点为不同的结点。

```java
class Solution {
    private int depthX = 0, depthY = 0;
    private TreeNode parentX = null, parentY = null;
    
    public boolean isCousins(TreeNode root, int x, int y) {
        dfs(root, null, x, y, 0);
        return (depthX == depthY) && (parentX != parentY);
    }
    
    private void dfs(TreeNode root, TreeNode parent, int x, int y, int depth) {
        if (root == null) return;
        
        // 如果x或y已经被找到，则没有必要基于当前结点继续DFS
        if (root.val == x) {
            depthX = depth;
            parentX = parent;
        } else if (root.val == y) {
            depthY = depth;
            parentY = parent;
        } else {
            dfs(root.left, root, x, y, depth + 1);
            dfs(root.right, root, x, y, depth + 1);
        }
    }
}
```