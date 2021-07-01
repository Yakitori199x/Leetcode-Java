# 236. Lowest Common Ancestor of a Binary Tree (2021.01.31)

https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
```

## Solution 1: DFS (Post-order, recursive)

- $TC:O(n)$
- $SC:O(h)$
- 对tree进行后序遍历，如果发现当前递归的root是p或q，则直接返回root。
- 如果p和q分布在LCA的两个子树中，则递归返回至LCA的时候left和right均不为null，返回root。
- 如果p或者q就是LCA，那么在找到该结点时直接返回，并且在之后的递归中left或right始终为null，递归结束时返回LCA。

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q) return root;
        
        TreeNode left = lowestCommonAncestor(root.left, p , q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        
        if (left != null && right != null) return root;
        return left == null ? right : left;
    }
}
```

## Solution 2: Record parents (Pre-order, iterative)

- $TC:O(n)$
- $SC:O(h)$
- 用迭代的方式前序遍历树，同时记录每个结点对应的父结点。比如，root的父结点为null，在将root.left压栈时设置其父结点为root等等。
- 持续遍历直至p和q都被记录在parent map中，然后用一个set来记录p到root的path。
- 最后从q开始，在q到root的path中找到第一个存在于前述set中的结点，即p和q的path的LCA。

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        Map<TreeNode, TreeNode> parent = new HashMap<>();
        Deque<TreeNode> stack = new LinkedList<>();
        
        parent.put(root, null);
        stack.push(root);
        
        while (!parent.containsKey(p) || !parent.containsKey(q)) {
            TreeNode cur = stack.pop();
            
            if (cur.left != null) {
                parent.put(cur.left, cur);
                stack.push(cur.left);
            }
            
            if (cur.right != null) {
                parent.put(cur.right, cur);
                stack.push(cur.right);
            }
        }
        
        Set<TreeNode> ancestors = new HashSet<>();
        while (p != null) {
            ancestors.add(p);
            p = parent.get(p);
        }
        
        while (!ancestors.contains(q)) {
            q = parent.get(q);
        }
        
        return q;
    }
}
```

## Solution 3: DFS (Post-order, iterative)

TBA