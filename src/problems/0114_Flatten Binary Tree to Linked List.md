## 114. Flatten Binary Tree to Linked List (2020.09.21)

https://leetcode.com/problems/flatten-binary-tree-to-linked-list/


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

## Solution 1: Iterative (pre-order)

- $TC:O(N)$
- $SC:O(h)$
- 由于本题要求in-place地进行flatten，所以单纯的前序遍历实现是不行的。
- 在迭代的过程中，需要把上一个遍历过的结点prev.right指向当前结点cur，这里引入了一个dummy node作为最初的prev结点。
- 也可以只维护一个cur结点，然后在压入左右子树结点之后判断栈是否非空，并将cur.right设置为nodes.peek()。

```java
class Solution {
    public void flatten(TreeNode root) {
        if (root == null) return ;
        
        Deque<TreeNode> nodes = new LinkedList<>();
        TreeNode cur = root, prev = new TreeNode(-1);
        
        nodes.push(cur);
        while (!nodes.isEmpty()) {
            cur = nodes.pop();
            
            if (cur.right != null) nodes.push(cur.right);
            if (cur.left != null) nodes.push(cur.left);
            
            prev.right = cur;
            prev.left = null;
            prev = cur;
        }
    }
}
```

## Solution 2: Recursive (post-order, left->right->root)

- $TC:O(N)$
- $SC:O(h)$
- 可以发现flatten之后的序列满足前序遍历，其逆序则满足left->right->root的后序遍历。
- 因此我们可以先对右子树递归，然后对左子树递归，最后处理root，需要一个prev来保存前一个递归的结点。

```java
class Solution {
    
    private TreeNode prev = null;
    
    public void flatten(TreeNode root) {
        if (root == null) return ;
        
        flatten(root.right);
        flatten(root.left);
        
        root.right = prev;
        root.left = null;
        prev = root;
    }
}
```

## Solution 3: Similar to Morris traversal

- $TC:O(N)$, 对于一个cur结点需要用while去寻找最右结点，但不影响整个过程每个结点至多被访问两次。
- $SC:O(1)$, 不需要额外的内存开销
- 先找到cur结点的左结点的最右结点，将该结点的right与cur结点的右子树连接，（若该结点还有左子树也不影响，在接下来的迭代中也会被flatten到正确的位置）
- 然后就是将cur结点的左子树转移到右侧，并把left设置为null。相当于就是把左子树的根结点到其最右结点的path给flatten到正确的位置了。
- 由于左子树的根结点有可能还有左子树，所以将cur=cur.right，继续迭代直至遍历所有结点。

```java
class Solution {
    public void flatten(TreeNode root) {
        if (root == null) return ;
        
        TreeNode cur = root;
        while (cur != null) {
            if (cur.left != null) {
                TreeNode temp = cur.left;
                while (temp.right != null) {
                    temp = temp.right;
                }
                
                temp.right = cur.right;
                cur.right = cur.left;
                cur.left = null;
            }
            
            cur = cur.right;
        }
    }
}
```

