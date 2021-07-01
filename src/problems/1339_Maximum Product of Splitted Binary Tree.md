# 1339. Maximum Product of Splitted Binary Tree (2021.05.24)

https://leetcode.com/problems/maximum-product-of-splitted-binary-tree/

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

## Solution 1: 2-pass DFS ver1

- $TC:O(n)$
- $SC:O(h)$
- 进行两遍DFS，第一遍求出整个树所有node的totalSum，那么去掉一条edge以后两个subTree的和的乘积为subSum * (totalSum - subSum)，subSum为其中某个subTree的结点和。因此，我们可以从每个node为根结点对应的subTree的subSum来求得最大的乘积，而这个subSum其实在进行DFS的过程中就求出来了。
- 所以，我们再次对root进行DFS求得最大的乘积。需要注意的是，我们在最终返回的时候才进行mod，否则可能造成实际上更大的res在mod之后得到更小的值的情况。

```java
class Solution {
    public int MOD = 1000000007;
    public long res = 0, totalSum = 0;
    
    public int maxProduct(TreeNode root) {
        totalSum = getTotalSum(root);
        calculateSubsumAndResult(root);
        return (int) (res % MOD);
    }
    
    private long getTotalSum(TreeNode root) {
        if (root == null) return 0;
        return root.val + getTotalSum(root.left) + getTotalSum(root.right);
    }
    
    private long calculateSubsumAndResult(TreeNode root) {
        if (root == null) return 0;
        long subSum = root.val + calculateSubsumAndResult(root.left) + calculateSubsumAndResult(root.right);
        res = Math.max(res, subSum * (totalSum - subSum));
        return subSum;
    }
}
```

## Solution 2: 2-pass DFS ver2

- $TC:O(n)$
- $SC:O(h)$
- 把Solution1中的求totalSum以及求subSum和result的两个方法集成为一个。
- 因为在计算totalSum的递归过程中，就已经计算出了以每个node为root的子树对应的subSum。
- 不过还是需要进行两边DFS，只是把实现进行了复用，看起来更加简洁。

```java
class Solution {
    public int MOD = 1000000007;
    public long res = 0, totalSum = 0;
    
    public int maxProduct(TreeNode root) {
        totalSum = calculateSumAndResult(root);
        calculateSumAndResult(root);
        return (int) (res % MOD);
    }
    
    private long calculateSumAndResult(TreeNode root) {
        if (root == null) return 0;
        long subSum = root.val + calculateSumAndResult(root.left) + calculateSumAndResult(root.right);
        res = Math.max(res, subSum * (totalSum - subSum));
        return subSum;
    }
}
```