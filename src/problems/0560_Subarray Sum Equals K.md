# 560. Subarray Sum Equals K (2021.03.19)

https://leetcode.com/problems/subarray-sum-equals-k/

## Solution 1: HashMap + PrefixSum

- $TC:O(n)$
- $SC:O(n)$
- Naive的方法是双重循环遍历数组，i:0->n-1，j:i->n-1，遍历的同时累积计算当前子数组[i,j]的元素和并判断，时间复杂度O(n^2)。
- 我们可以利用HashMap和前缀和来优化，注意这里不是求sum为k的最长子数组，而是求sum为k的子数组的数量（出现了几次），但方法是类似的。

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        int prefixSum = 0, res = 0;
        Map<Integer, Integer> seenSum = new HashMap<>();
        // 便利地处理prefixSum = k的情况
        seenSum.put(0, 1);
        for (int i = 0; i < nums.length; i++) {
            prefixSum += nums[i];
            if (seenSum.containsKey(prefixSum - k)) {
                res += seenSum.get(prefixSum - k);
            }
            seenSum.put(prefixSum, seenSum.getOrDefault(prefixSum, 0) + 1);
        }
        return res;
    }
}
```