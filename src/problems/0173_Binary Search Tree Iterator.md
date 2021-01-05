## 173. Binary Search Tree Iterator (2020.06.15)

https://leetcode.com/problems/binary-search-tree-iterator/


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

 /**
 * Your BSTIterator object will be instantiated and called as such:
 * BSTIterator obj = new BSTIterator(root);
 * int param_1 = obj.next();
 * boolean param_2 = obj.hasNext();
 */
```

## Solution 1

- $TC:next() -> O(1), hasNext() -> O(1)$, but constructor costs O(h) in average because of inorder traversal
- $SC:O(N)$, need a list to save the traversal result
- The iterator of BST is essentially based on the inorder traversal of the tree
- We can do inorder traversal when the iterator is initialized and save the sequence in a list, then we can use a pointer to iterate throught the result list

```java
class BSTIterator {

    private List<Integer> nodesInOrder;
    private int index;
    
    public BSTIterator(TreeNode root) {
        this.nodesInOrder = new ArrayList<>();
        this.index = 0;
        inorder(root, this.nodesInOrder);
    }
    
    private void inorder(TreeNode root, List<Integer> nodes) {
        if (root == null) return;
        
        inorder(root.left, nodes);
        nodes.add(root.val);
        inorder(root.right, nodes);
    }
    
    /** @return the next smallest number */
    public int next() {
        return nodesInOrder.get(index++);
    }
    
    /** @return whether we have a next smallest number */
    public boolean hasNext() {
        return (index != nodesInOrder.size());
    }
}
```

## Solution 2

- $TC:next() -> O(N)$, when iterate through the whole tree
- $TC:hasNext() -> O(1)$, comply with the requirement that cost is O(1) in average
- $SC:O(h)$, stack stores at most h nodes
- Stack -- break the stack-based inorder traversal into 2 steps

```java
class BSTIterator {

    Stack<TreeNode> stack;
    
    public BSTIterator(TreeNode root) {
        this.stack = new Stack<>();
        
        leftMostTraverse(root);
    }
    
    private void leftMostTraverse(TreeNode root) {
        while (root != null) {
            stack.push(root);
            root = root.left;
        }
    }
    
    /** @return the next smallest number */
    public int next() {
        TreeNode minNode = stack.pop();
        leftMostTraverse(minNode.right);
        return minNode.val;
    }
    
    /** @return whether we have a next smallest number */
    public boolean hasNext() {
        return !stack.isEmpty();
    }
}
```

