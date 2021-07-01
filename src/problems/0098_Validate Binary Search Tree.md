# 98. Validate Binary Search Tree (2021.03.02)

https://leetcode.com/problems/validate-binary-search-tree/

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

## Solution 1: In-order traversal, Iterative

- $TC:O(N)$
- $SC:O(h)$
- 因为二叉搜索树的中序遍历一定是一个递增数列，所以要判断一棵树是否是有效的BST，我们可以对其进行中序遍历。
- 然后检查获得的中序遍历序列是否为递增数列即可。
- 当然，这样由于排序的开销和额外存储的序列会增加复杂度，我们可以维护一个prev来始终指向当前正在被遍历的结点的前一个结点（中序遍历意义上的）。
- 之后比较prev和cur的val即可。

```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        if (root == null) return true;
        
        Deque<TreeNode> stack = new LinkedList<>();
        TreeNode cur = root, prev = null;
        while (cur != null || !stack.isEmpty()) {
            // 一路向左遍历到leftmost结点
            while (cur != null) {
                stack.push(cur);
                cur = cur.left;
            }
            
            cur = stack.pop();
            if (prev != null && prev.val >= cur.val) return false;
            // 设定prev为当前结点后，继续遍历下去
            prev = cur;
            cur = cur.right;
        }
        
        return true;
    }
}
```

## Solution 2: In-order traversal, Recursive

- $TC:O(N)$
- $SC:O(h)$
- Solution1的递归实现，用一个全局变量来保存prev比较便于实现。

```java
class Solution {
    private TreeNode prev = null;
    
    public boolean isValidBST(TreeNode root) {
        if (root == null) return true;

        // 检查左子树
        if (!isValidBST(root.left)) return false;
        // 检查当前结点，如果有效则设置当前结点为prev
        if (prev != null && prev.val >= root.val) return false;
        prev = root;
        // 检查右子树
        if (!isValidBST(root.right)) return false;
        
        return true;
    }
}
```

## Solution 3: Min-max

- $TC:O(N)$
- $SC:O(h)$
- BST的每个结点的取值都会被锁定在一个范围内（根结点除外）。
- 比如题目实例中的[5,1,4,null,null,3,6]这棵树，根结点的可取值范围是负无穷到正无穷，也就是没有特殊限制。
- 但是对于4这个结点来说，作为根结点的右结点，其合理的范围是[root.val(5),+]，很明显这里4不符合这个范围。
- 同样的，3这个结点的取值范围应该是[root.val,root.right.val]，以此类推。
- 我们只需要确定每个结点的取值范围min和max，然后判断当前结点的值是否在这个范围内即可。

```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        return isValidBST(root, null, null);
    }
    
    // 这里使用的是TreeNode来存储min，max
    // 主要是因为本题的结点值是-2^31 <= Node.val <= 2^31 - 1，刚好到Integer.MIN_VALUE和Integer.MAX_VALUE
    // 如果直接使用这两个值作为初始边界递归下去的话，遇到了结点值为-2^31和2^31 - 1的情况就算合理也会判定为false（因为等于的情况也不行）
    // 比如[2^31 - 1]或是[-2^31,null,2^31 - 1]等。当然也可以扩大范围用Long去算。
    private boolean isValidBST(TreeNode root, TreeNode min, TreeNode max) {
        if (root == null) return true;
        
        if (min != null && root.val <= min.val) return false;
        
        if (max != null && root.val >= max.val) return false;
        
        return isValidBST(root.left, min, root) && isValidBST(root.right, root, max);
    }
}
```