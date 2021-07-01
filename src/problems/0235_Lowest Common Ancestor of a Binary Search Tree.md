# 235. Lowest Common Ancestor of a Binary Search Tree (2021.01.27)

https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/

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

## Solution 1: Iterative

- $TC:O(h)$, worst is $O(n)$ for skewed tree, where h = n
- $SC:O(1)$
- 根据LCA的定义，我们可以知道LCA要不然就是p或者q的其中一个，要不然就是p和q分别分布在其左右子树中
- 因为BST有其特殊的性质，我们可以很方便地判断p和q相对于当前结点的位置
- 当(p.val - cur.val)和(q.val - cur.val)的乘积大于0时，说明p，q位于cur的同一侧，那么就需要根据p或q的大小来移动cur
- 否则，说明p和q分别位于cur的左右子树，或是cur就是p或q

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || p == null || q == null) return null;
        
        TreeNode cur = root;
        while ((p.val - cur.val) * (q.val - cur.val) > 0) {
            cur = (p.val < cur.val) ? cur.left : cur.right;
        }
        
        return cur;
    }
}
```

## Solution 2: Recursive

- $TC:O(h)$, worst is $O(n)$ for skewed tree, where h = n
- $SC:O(h)$, worst is $O(n)$ for skewed tree, where h = n

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || p == null || q == null) return null;
        
        if ((p.val - root.val) * (q.val - root.val) <= 0) return root;
        
        if (p.val < root.val) {
            return lowestCommonAncestor(root.left, p, q);
        } else {
            return lowestCommonAncestor(root.right, p, q);
        }
    }
}
```