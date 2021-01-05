## 144. Binary Tree Preorder Traversal (2020.06.11)

https://leetcode.com/problems/binary-tree-preorder-traversal/


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

## Solution 1: Recursion

- $TC:O(N)$
  1. every node will be traversed with constant operation
  2. recursive function is T(n) = 2 * T(n/2) + 1
- $SC:O(N)$
  1. f we don't consider the cost of return list, it should be O(n) but O(logn) in average
  2. Because the cost of call stack in recursion

```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        preorderHelper(root, res);
        return res;
    }
    
    private void preorderHelper(TreeNode root, List<Integer> res) {
        if (root == null) return;
        
        res.add(root.val);
        preorderHelper(root.left, res);
        preorderHelper(root.right, res);
    }
}
```

## Solution 2: Stack

- $TC:O(N)$
- $SC:O(N)$
- 前序遍历的顺序是(根节点 -> 左子树 -> 右子树)
- 所以只需要把current节点向左子树移动，同时在移动前将右子树的节点存入stack中以备后续遍历即可

```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        Stack<TreeNode> stack = new Stack<>();
        TreeNode current = root;
        
        while (current != null || !stack.isEmpty()) {
            
            if (current == null) {
                current = stack.pop();
            } else {
                res.add(current.val);
                stack.push(current.right);
                current = current.left;
            }
        }
        
        return res;
    }
}
```

## Solution 3: Morris Traversal

- $TC:O(N)$
- $SC:O(N)$
  1. If we don't consider the cost of return list, it should be O(1) in average
  2. Except the case all the nodes are connected as a line by left references
- 前序遍历同样可以使用Morris Traversal来实现
- 与中序遍历的区别在于根节点需要先于左子树被遍历
- 也就是说在找到左子树的最右节点时，需要先把根节点加入list，再进行左移
- 而在检测到形成闭环的情况下(pre.right == current)则只需要将闭环解除并右移即可

```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        TreeNode current = root;
        
        while (current != null) {
            if (current.left != null) {
                TreeNode pre = current.left;
                
                while (pre.right != null && pre.right != current) {
                    pre = pre.right;
                }
                
                if (pre.right == null) {
                    pre.right = current;
                    res.add(current.val);
                    current = current.left;
                } else {
                    pre.right = null;
                    current = current.right;
                }
            } else {
                res.add(current.val);
                current = current.right;
            }
        }
        
        return res;
    }
}
```

