## 94. Binary Tree Inorder Traversal (2020.06.08)

https://leetcode.com/problems/binary-tree-inorder-traversal/


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
  2. recursive function is $T(n)=2*T(n/2) + 1$
- $SC:O(N)$
  1. If we don't consider the cost of return list, it should be O(n) but O(logn) in average
  2. Because the cost of call stack in recursion

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        inorderHelper(root, res);
        return res;
    }
    
    private void inorderHelper(TreeNode root, List<Integer> res) {
        if (root == null) return;
        
        inorderHelper(root.left, res);
        res.add(root.val);
        inorderHelper(root.right, res);
    }
}
```

## Solution 2: Stack

- $TC:O(N)$
- $SC:O(N)$

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        Stack<TreeNode> stack = new Stack<>();
        TreeNode current = root;
        
        while (current != null || !stack.isEmpty()) {
            while (current != null) {
                stack.push(current);
                current = current.left;
            }
            
            current = stack.pop();
            res.add(current.val);
            current = current.right;
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
- 在进行中序遍历时，root会在左子树的最右叶子节点之后被遍历
- 因此，当左子树为空时，存储root的value，直接往右侧遍历
- 而当左子树非空时，找到左子树的最右节点，将该节点的右引用指向root，即可确保root以正确的顺序被遍历
- 当然，该操作会导致闭环的形成，所以在寻找左子树最右节点的情况下，需要判断是否返回到起始节点

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
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
                    current = current.left;
                } else {
                    pre.right = null;
                    res.add(current.val);
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

