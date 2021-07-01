# 979. Distribute Coins in Binary Tree (2021.05.23)

https://leetcode.com/problems/distribute-coins-in-binary-tree/

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

## Solution 1: DFS - global var

- $TC:O(n)$
- $SC:O(n)$，当tree平衡时为O(logn)
- 该题的思路其实就是bottom-up地检查每个node当前的coins，如果coins的数量超过1个，说明node有多余的coins可以分摊到其他的node。
- 如果coins的数量为0，说明node还缺少1个coin，这种情况下我们就可以给这个node预支一个coin。
- 不管是有多余的情况还是有不足的情况，都会造成向parent去（多余的均移动到parent）或是从parent来（不论parent是否足够，提前预支）的moves，所以我们需要将对应的left和right的绝对值累积到moves中。
- 同时，对于parent来说，不仅要考虑其左右子树中coins多余和不足的情况，还要考虑parent自身的coins（要留下一个给parent自己），所以最终返回给父结点的结果就是left + right + root.val - 1，表示parent的父结点的左/右子树的coins状况。

```java
class Solution {
    private int moves = 0;
    
    public int distributeCoins(TreeNode root) {
        dfs(root);
        return moves;
    }
    
    private int dfs(TreeNode root) {
        if (root == null) return 0;
        int left = dfs(root.left), right = dfs(root.right);
        moves += (Math.abs(left) + Math.abs(right));
        return left + right + root.val - 1;
    }
}
```

## Solution 2: DFS - no helper

- $TC:O(n)$
- $SC:O(n)$，当tree平衡时为O(logn)
- 与Solution1思路一致，不过该解法直接把left和right对应多余/不足的coins修改在root的val上。

```java
class Solution {
    public int distributeCoins(TreeNode root) {
        if (root == null) return 0;
        int res = 0;
        
        if (root.left != null) {
            // 累积root的左子树递归后返回的所需moves
            res += distributeCoins(root.left);
            // 将左子树多余/不足的coins累积到root上
            root.val += root.left.val - 1;
            // 将左子树多余/不足的coins都需要moves
            res += Math.abs(root.left.val - 1);
        }
        
        if (root.right != null) {
            res += distributeCoins(root.right);
            root.val += root.right.val - 1;
            res += Math.abs(root.right.val - 1);
        }
        
        return res;
    }
}
```

## Solution 3: DFS - ver3

- $TC:O(n)$
- $SC:O(n)$，当tree平衡时为O(logn)

```java
class Solution {
    public int distributeCoins(TreeNode root) {
        return distributeCoins(root, null);
    }
    
    private int distributeCoins(TreeNode root, TreeNode parent) {
        if (root == null) return 0;
        int left = distributeCoins(root.left, root);
        int right = distributeCoins(root.right, root);
        if (parent != null) parent.val += root.val - 1;
        return Math.abs(root.val - 1) + left + right;
    }
}
```