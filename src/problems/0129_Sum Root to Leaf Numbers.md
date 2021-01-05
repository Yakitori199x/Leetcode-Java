## 129. Sum Root to Leaf Numbers (2020.09.30)

https://leetcode.com/problems/sum-root-to-leaf-numbers/


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

## Solution 1: DFS - Iterative - Preorder

- $TC:O(N)$
- $SC:O(h)$
- 按照前序遍历进行DFS
- 往栈中压入子结点时，将当前路径构成的数字乘以10，再加上子结点的值，即从root到该子结点构成的数
- 如果遇到叶子结点，直接将其对应的num加入最终结果

```java
class Solution {
    public int sumNumbers(TreeNode root) {
        if (root == null) return 0;
        
        Deque<TreeNode> nodes = new LinkedList<>();
        Deque<Integer> nums = new LinkedList<>();
        int sum = 0;
        nodes.push(root);
        nums.push(root.val);
        
        while (!nodes.isEmpty()) {
            TreeNode node = nodes.pop();
            int num = nums.pop();
            
            if (node.left == null && node.right == null) sum += num;
            
            if (node.right != null) {
                nodes.push(node.right);
                nums.push(10 * num + node.right.val);
            }
            
            if (node.left != null) {
                nodes.push(node.left);
                nums.push(10 * num + node.left.val);
            }
        }
        
        return sum;
    }
}
```

## Solution 2: DFS - Recursive

- $TC:O(N)$
- $SC:O(h)$

```java
class Solution {
    public int sumNumbers(TreeNode root) {
        return getSum(root, 0);
    }
    
    private int getSum(TreeNode root, int preNum) {
        if (root == null) return 0;
        
        int curNum = 10 * preNum + root.val;
        if (root.left == null && root.right == null) return curNum;
        
        return getSum(root.left, curNum) + getSum(root.right, curNum);
    }
}
```

## Solution 3: DFS - Recursive (set a field to store sum)

- $TC:O(N)$
- $SC:O(h)$

```java
class Solution {
    private int sum = 0;
    
    public int sumNumbers(TreeNode root) {
        getSum(root, 0);
        return sum;
    }
    
    private void getSum(TreeNode root, int preNum) {
        if (root == null) return ;
        
        int curNum = 10 * preNum + root.val;
        if (root.left == null && root.right == null) sum += curNum;
        
        getSum(root.left, curNum);
        getSum(root.right, curNum);
    }
}
```

