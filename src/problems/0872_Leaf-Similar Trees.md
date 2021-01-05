## 872. Leaf-Similar Trees (2020.11.03)

https://leetcode.com/problems/leaf-similar-trees/


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

## Solution1: Simple DFS

- $TC: O(max(n_{1}, n_{2}))$
- $SC:O(max(n_{1},n_{2}))$
- 分别对两棵树进行DFS，获得叶子结点的List并进行比较

```java
class Solution {
    public boolean leafSimilar(TreeNode root1, TreeNode root2) {
        if (root1 == null || root2 == null) return false;
        
        List<Integer> leafList1 = new ArrayList<>(), leafList2 = new ArrayList<>();
        dfs(root1, leafList1);
        dfs(root2, leafList2);
        
        return leafList1.equals(leafList2);
    }
    
    private void dfs(TreeNode root, List<Integer> leafList) {
        if (root == null) return ;
        if (root.left == null && root.right == null) leafList.add(root.val);
        
        dfs(root.left, leafList);
        dfs(root.right, leafList);
    }
}
```

## Solution2: Generate one leaf node and compare

- $TC: O(max(n_{1}, n_{2}))$
- $SC:O(max(h_{1},h_{2}))$
- 对两棵树进行中序遍历，每找到一个叶子结点就返回并进行比较

```java
class Solution {
    public boolean leafSimilar(TreeNode root1, TreeNode root2) {
        if (root1 == null || root2 == null) return false;
        
        Deque<TreeNode> s1 = new LinkedList<>(), s2 = new LinkedList<>();
        s1.push(root1);
        s2.push(root2);
        
        while (!s1.isEmpty() && !s2.isEmpty()) {
            // 进行inorder traversal，每当遇到leaf就返回并比较
            // 这样可以确保每次比较的都是root1和root2的第n个叶子结点
            // 如果叶子结点的值相等且栈非空，则继续根据栈中保存的信息进行遍历
            if (dfs(s1) != dfs(s2)) return false;
        }
        
        return s1.isEmpty() && s2.isEmpty();
    }
    
    private int dfs(Deque<TreeNode> stack) {
        while (true) {
            TreeNode node = stack.pop();
            if (node.left != null) stack.push(node.left);
            if (node.right != null) stack.push(node.right);
            
            if (node.left == null && node.right == null) return node.val;
        }
    }
}
```

