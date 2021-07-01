# 508. Most Frequent Subtree Sum (2021.04.29)

https://leetcode.com/problems/most-frequent-subtree-sum/

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

## Solution 1: HashMap + Post-order

- $TC:O(n)$
- $SC:O(n)$
- 通过后序遍历求出所有的subtree sum，并将sum及其对应出现次数保存在map中，同时记录下maxCount。
- 最后遍历map的所有元素，把出现次数等于maxCount的sum加入到res中。

```java
class Solution {
    private Map<Integer, Integer> count = new HashMap<>();
    private int maxCount = 0;
    
    public int[] findFrequentTreeSum(TreeNode root) {
        if (root == null) return null;
        postOrder(root);
        
        List<Integer> res = new ArrayList<>();
        for (int sum : count.keySet()) {
            if (count.get(sum) == maxCount) res.add(sum);
        }
        return res.stream().mapToInt(Integer::valueOf).toArray();
    }
    
    private int postOrder(TreeNode root) {
        if (root == null) return 0;
        int left = postOrder(root.left), right = postOrder(root.right);
        int sum = left + right + root.val;
        count.put(sum, count.getOrDefault(sum, 0) + 1);
        maxCount = Math.max(maxCount, count.get(sum));
        return sum;
    }
}
```