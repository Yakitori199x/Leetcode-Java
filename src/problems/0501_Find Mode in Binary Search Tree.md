# 501. Find Mode in Binary Search Tree (2021.03.03)

https://leetcode.com/problems/find-mode-in-binary-search-tree/

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

## Solution 1: DFS, inorder, iterative, real O(1)

- $TC:O(N)$
- $SC:O(1)$
- 真正的O(1)解法，通过第一次中序遍历记录BST中modes的出现次数（即出现频率最高的结点值，可能同时存在多个modes）。
- 然后再次进行中序遍历将出现频率达到maxCount的结点值加入最终答案，这些结点值就是我们要求的mode。

```java
class Solution {
    private int maxCount = 1;
    private List<Integer> res = new ArrayList<>();
    
    public int[] findMode(TreeNode root) {    
        inorder(root, false);
        inorder(root, true);
        
        return res.stream().mapToInt(Integer::intValue).toArray();
    }
    
    private void inorder(TreeNode root, boolean getList) {
        if (root == null) return;
        Deque<TreeNode> stack = new LinkedList<>();
        TreeNode prev = null;
        int curCount = 1;
        while (root != null || !stack.isEmpty()) {
            while (root != null) {
                stack.push(root);
                root = root.left;
            }
            
            root = stack.pop();
            if (prev != null && prev.val == root.val) {
                curCount++;
                if (curCount > maxCount) maxCount = curCount;
            } else {
                curCount = 1;
            }
            
            if (getList && curCount == maxCount) res.add(root.val);
            
            prev = root;
            root = root.right;
        }
    }
}
```

## Solution 2: DFS, inorder, recursive

- $TC:O(N)$
- $SC:O(N)$
- 该解法动态地维护当前maxCount下的所有modes，但是并不是真正的O(1)。
- 因此存在这样一种情况[1, 2, 3, 4, 5, 6, 7, 8, ..., n - 1, n, n]。
- 即从[1, 2, 3, 4, 5, 6, 7, 8, ..., n - 1]的maxCount均为1，且这些值都被认定为modes。
- 但是在遍历到最后两个n的时候，发现maxCount应为2，之前的modes被作废重置，但空间复杂度在之前已经达到了O(N)。

```java
class Solution {
    // 维护一些全局变量更容易实现递归版本
    private int maxCount = 1;
    private int curCount = 1;
    private TreeNode prev = null;
    private List<Integer> res = new ArrayList<>();
    
    public int[] findMode(TreeNode root) {    
        inorder(root);
        return res.stream().mapToInt(Integer::intValue).toArray();
    }
    
    private void inorder(TreeNode root) {
        if (root == null) return;
        
        inorder(root.left);
        
        if (prev != null && prev.val == root.val) {
            curCount++;
            // 当发现curCount大于maxCount时
            // 更新maxCount，同时说明之前保存的modes无效，初始化res
            if (curCount > maxCount) {
                maxCount = curCount;
                res = new ArrayList<>();
            }
        } else {
            curCount = 1;
        }
        
        if (curCount == maxCount) res.add(root.val);
        
        prev = root;
        
        inorder(root.right);
    }
}
```

## Solution 3: Morris traversal
- 这也是一种O(1)算法，以后有空可以补全