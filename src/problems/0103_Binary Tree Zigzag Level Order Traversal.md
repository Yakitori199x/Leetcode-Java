## 103. Binary Tree Zigzag Level Order Traversal (2020.06.22)

https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/


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

## Solution 1: Queue + BFS

- $TC:O(N)$
- $SC:O(N)$

```java
class Solution {
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) return res;
        
        Queue<TreeNode> q = new LinkedList<>();
        q.add(root);
        // Use a flag variable to record traverse order
        // true -> left to right; false -> right to left
        boolean zigzag = true;
        
        while (!q.isEmpty()) {
            int size = q.size();
            // Use LinkedList to save time cost for adding from head
            List<Integer> nodes = new LinkedList<>();
            for (int i = 0; i < size; i++) {
                TreeNode node = q.remove();
                if (zigzag) {
                    nodes.add(node.val);
                } else {
                    // when flag is false, add from the front
                    nodes.add(0, node.val);
                }
                
                // always keep the queue to save in the order of left to right
                if (node.left != null) q.add(node.left);
                if (node.right != null) q.add(node.right);
            }
            
            res.add(nodes);
            zigzag = !zigzag;
        }
        
        return res;
    }
}
```

## Solution 2: Queue + DFS

- $TC:O(N)$
- $SC:O(N)$

```java
class Solution {
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        // overload
        // use level to control the direction
        // level is also used to get list of values of corresponding level
        zigzagLevelOrder(root, res, 0);
        return res;
    }
    
    private void zigzagLevelOrder(TreeNode root, List<List<Integer>> res, int level) {
        if (root == null) return;
        
        if (res.size() <= level) res.add(new LinkedList<>());
        
        List<Integer> nodes = res.get(level);
        if (level % 2 == 0) {
            nodes.add(root.val);
        } else {
            nodes.add(0, root.val);
        }
        
        zigzagLevelOrder(root.left, res, level + 1);
        zigzagLevelOrder(root.right, res, level + 1);
    }
}
```

## Solution 3: Two Stacks

- $TC:O(N)$
- $SC:O(N)$

```java
class Solution {
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) return res;
        
        // s1 for nodes of current level, s2 for storing nodes of next level
        Stack<TreeNode> s1 = new Stack<>();
        Stack<TreeNode> s2 = new Stack<>();
        boolean zigzag = true;
        s1.push(root);
        
        while (!s1.isEmpty()) {
            int size = s1.size();
            List<Integer> nodes = new ArrayList<>();
            
            for (int i = 0; i < size; i++) {
                TreeNode node = s1.pop();
                nodes.add(node.val);
                
                if (zigzag) {
                    if (node.left != null) s2.push(node.left);
                    if (node.right != null) s2.push(node.right);
                } else {
                    if (node.right != null) s2.push(node.right);
                    if (node.left != null) s2.push(node.left);
                }
            }
            
            res.add(nodes);
            zigzag = !zigzag;
            // remember to swap two stacks to keep s1 full of new nodes when entering next iteration
            s1 = s2;
            s2 = new Stack<>();
        }
        
        return res;
    }
}
```

