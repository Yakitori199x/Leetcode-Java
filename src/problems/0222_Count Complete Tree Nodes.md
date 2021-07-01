# 222. Count Complete Tree Nodes (2021.03.26)

https://leetcode.com/problems/count-complete-tree-nodes/

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

## Solution 1: Recursive, Tree's Binary Search

- $TC:O(logn * logn)$，因为是一个complete二叉树，所以每次求高度的开销为O(logn)。然后每次都只探索半边的子树，总共进行O(logn)次探索。
- $SC:O(logn)$
- 我们可以知道，在本题的情况下，最底层的结点数要么超过最大可能结点数的一半（说明左子树是高度为h-1的满树）。
- 要么不足最大可能结点数的一半（说明右子树是高度为h-2的满树，左子树还未完全填满）。
- 因此，对于每个root，我们可以先求出以root为根的树的高度h，然后再求出当前root的右子树高度hr。
- 如果hr等于h-1，说明root的左右子树高度一致，那么左子树肯定是全满树（包含root的情况下结点数为2^h），我们继续以相同的方法向右子树探索。
- 如果hr不等于h-1（也就是等于h-2），说明左子树比右子树高且右子树为全满树（包含root的情况下结点数为2^(h-1)），这种情况下我们则向左子树探索。

```java
class Solution {
    public int countNodes(TreeNode root) {
        int h = height(root);
        if (h == -1) return 0;
        int hr = height(root.right);
        return hr == h - 1 ? (1 << h) + countNodes(root.right) : (1 << (h - 1)) + countNodes(root.left);
    }
    
    private int height(TreeNode root) {
        if (root == null) return -1;
        return 1 + height(root.left);
    }
}
```

## Solution 2: Iterative, Tree's Binary Search

- $TC:O(logn * logn)$
- $SC:O(logn)$，求高度的时候不用递归而是不断向左子树循环则可将开销降低至O(1)。
- Solution1的迭代实现。

```java
class Solution {
    public int countNodes(TreeNode root) {
        int nums = 0;
        while (root != null) {
            int h = height(root), hr = height(root.right);
            if (hr == h - 1) {
                nums += (1 << h);
                root = root.right;
            } else {
                nums += (1 << (h - 1));
                root = root.left;
            }
        }
        return nums;
    }
    
    private int height(TreeNode root) {
        if (root == null) return -1;
        return 1 + height(root.left);
    }
}
```

## Solution 3: Count both

- $TC:O(logn * logn)$
- $SC:O(logn)$
- 这种解法的思路就是同时计算当前root下，最左和最右的Path对应的高度，如果两者相等，说明当前的树是全满的，那么直接返回结点数即可。
- 否则，说明左子树或者右子树不是全满的，对左子树和右子树分别进行相同的操作，返回1（根结点）+左子树的结点数+右子树的结点数。
- 这种方法乍看之下是遍历的所有的结点，但可以发现，左右子树其中必有一个是全满的，对于全满的那一半，只会再进行一次O(logn)的操作就不继续递归下去了。

```java
class Solution {
    public int countNodes(TreeNode root) {
        if (root == null) return 0;
        TreeNode l = root, r = root;
        
        // 由于可以确定左边的高度会大于或等于右边的高度
        // 所以可以只保留一个height并同时往下迭代
        int height = 0;
        while (r != null) {
            l = l.left;
            r = r.right;
            height++;
        }
        
        // 如果l为空，说明左右两条Path的长度是一致的，当前为全满树
        if (l == null) return (1 << height) - 1;
        return 1 + countNodes(root.left) + countNodes(root.right);
        
    }
}
```