# 865. Smallest Subtree with all the Deepest Nodes (2021.03.11)

https://leetcode.com/problems/smallest-subtree-with-all-the-deepest-nodes/

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

## Solution 1: DFS ver1

- $TC:O(n)$
- $SC:O(h)$
- Pair用来保存以对应结点为根结点的情况下，树的最大深度以及最小的subtreeWithDeepestNodes。
- 比如left就是当前root的左子树的最大深度以及包含了左子树所有deepestNodes的最小子树的根结点。
- 如果d1=d2（即左右子树最大深度相等），那么说明当前root对应的树可以作为新的囊括了所有deepestNodes的子树。
- 否则，返回较深的那一侧的subtree。

```java
class Solution {
    public TreeNode subtreeWithAllDeepest(TreeNode root) {
        return dfs(root).getValue();
    }
    
    private Pair<Integer, TreeNode> dfs(TreeNode root) {
        if (root == null) return new Pair(0, null);
        Pair<Integer, TreeNode> left = dfs(root.left), right = dfs(root.right);
        
        int d1 = left.getKey(), d2 = right.getKey();
        TreeNode r1 = left.getValue(), r2 = right.getValue();
        return new Pair(Math.max(d1, d2) + 1, d1 == d2? root : d1 > d2 ? r1 : r2);
    }
}
```

## Solution 2: DFS ver2

- $TC:O(n)$
- $SC:O(h)$
- 跟Solution1的思路类似，都是通过DFS来追踪depth，并判断左右子树的深度是否相等来找到最小subtree。
- 不同的是Solution1返回的Pair中包含了局部最优的根结点，最终会在root处判断并决定返回的是left/right/root。
- 但该解法只能返回depth，就可能出现在遍历右子树时即使不是最深的，也会把左子树的res覆盖掉的情况。
- 所以，我们还需要track一个全局的deepest，只有满足left=right=deepest才能对res进行更新。

```java
class Solution {
    private TreeNode res = null;
    private int deepest = 0;
    
    public TreeNode subtreeWithAllDeepest(TreeNode root) {
        dfs(root, 0);
        return res;
    }
    
    private int dfs(TreeNode root, int level) {
        if (root == null) return level;
        
        int leftDepth = dfs(root.left, level + 1), rightDepth = dfs(root.right, level + 1);
        
        int curDepth = Math.max(leftDepth, rightDepth);
        deepest = Math.max(deepest, curDepth);
        if (leftDepth == deepest && rightDepth == deepest) res = root;
        return curDepth;
    } 
}
```

## Solution 3: BFS + LCA

- $TC:O(n)$
- $SC:O(n)$
- 先通过BFS层次遍历整棵树，这样就可以获得最后一层的所有结点。
- 那么接下来我们要如何找到包含了所有最深层结点的最小子树的根结点呢？
- 其实，我们不需要关注最深层的所有结点。
- 我们需要做的就是track最深层的leftmost和rightmost结点，然后找出它们的LCA即可。

```java
class Solution {
    public TreeNode subtreeWithAllDeepest(TreeNode root) {
        if (root == null) return root;
        
        Queue<TreeNode> queue = new LinkedList<>();
        TreeNode leftmost = null, rightmost = null;
        queue.offer(root);
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                TreeNode cur = queue.poll();
                if (i == 0) leftmost = cur;
                if (i == size - 1) rightmost = cur;
                if (cur.left != null) queue.offer(cur.left);
                if (cur.right != null) queue.offer(cur.right);
            }
        }
        
        return findLCA(root, leftmost, rightmost);
    }
    
    private TreeNode findLCA(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q) return root;
        
        TreeNode left = findLCA(root.left, p, q);
        TreeNode right = findLCA(root.right, p, q);
        if (left != null && right != null) return root;
        return left == null ? right : left;
    }
}
```