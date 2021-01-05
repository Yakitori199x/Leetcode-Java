## 112. Path Sum (2020.09.02)

https://leetcode.com/problems/path-sum/


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

## Solution 1: DFS (Iterative)

- $TC:O(N)$
- $SC:O(h)$
- Preorder iteration，每个结点对应在sums stack里记录的是根结点到该结点的pathSum（也可以反过来用递减来做）

```java
class Solution {
    public boolean hasPathSum(TreeNode root, int sum) {
        if (root == null) return false;
        
        Deque<TreeNode> nodes = new LinkedList<>();
        Deque<Integer> sums = new LinkedList<>();
        
        nodes.push(root);
        sums.push(root.val);
        while (!nodes.isEmpty()) {
            TreeNode temp = nodes.pop();
            int tempSum = sums.pop();
            
            if (temp.left == null && temp.right == null && tempSum == sum) return true;
            
            if (temp.left != null) {
                nodes.push(temp.left);
                sums.push(temp.left.val + tempSum);
            }
            
            if (temp.right != null) {
                nodes.push(temp.right);
                sums.push(temp.right.val + tempSum);
            }
        }
        
        return false;
    }
}
```

## Solution 2: DFS (Recursive)

- $TC:O(N)$
- $SC:O(h)$
- 这题如果按照求深度那样对递归的返回值加一（也就是从叶子往上累加）肯定是行不通的，因为我们需要的是对每个可能的从根到叶子的距离和进行判断。所以，我们可以想到只要对左右结点递归的时候，传入的值改变为当前sum和当前结点的val之差即可。
- 即：求根结点到叶子结点是否存在pathSum等于sum，也就是求左子树或右子树的根到叶子是否存在pathSum等于sum-root.val。

```java
class Solution {
    public boolean hasPathSum(TreeNode root, int sum) {
        if (root == null) return false;
        if (root.left == null && root.right == null) return root.val == sum;
        
        return hasPathSum(root.left, sum - root.val) || hasPathSum(root.right, sum - root.val);
    }
}
```

## Solution 3: BFS

- $TC:O(N)$
- $SC:O(N)$

```java
class Solution {
    public boolean hasPathSum(TreeNode root, int sum) {
        if (root == null) return false;
        
        Queue<TreeNode> nodes = new LinkedList<>();
        Queue<Integer> sums = new LinkedList<>();
        
        nodes.offer(root);
        sums.offer(sum);
        while (!nodes.isEmpty()) {
            int size = nodes.size();
            for (int i = 0; i < size; i++) {
                TreeNode temp = nodes.poll();
                int tempSum = sums.poll();
                if (temp.left == null && temp.right == null && temp.val == tempSum) return true;
                
                if (temp.left != null) {
                    nodes.offer(temp.left);
                    sums.offer(tempSum - temp.val);
                }
                
                if (temp.right != null) {
                    nodes.offer(temp.right);
                    sums.offer(tempSum - temp.val);
                }
            }
        }
        
        return false;
    }
}
```

## Solution 4: DFS (Iterative)

- $TC:O(N)$
- $SC:O(h)$
- Postorder iteration

```java
class Solution {
    public boolean hasPathSum(TreeNode root, int sum) {
        if (root == null) return false;
        
        Deque<TreeNode> nodes = new LinkedList<>();
        int sums = 0;
        TreeNode temp = root, pre = null;
        
        while (temp != null || !nodes.isEmpty()) {
            while (temp != null) {
                nodes.push(temp);
                sums += temp.val;
                temp = temp.left;
            }
            
            temp = nodes.peek();
            if (temp.right == null || pre == temp.right) {
                if (temp.left == null && temp.right == null && sums == sum) return true;
                temp = nodes.pop();
                pre = temp;
                sums -= temp.val;
                temp = null;
            } else {
                temp = temp.right;
            }
        }
        
        return false;
    }
}
```

