# 1191. K-Concatenation Maximum Sum (2021.05.09)

https://leetcode.com/problems/k-concatenation-maximum-sum/

## Solution 1: Kadane's Algo + Prefix/Suffix Sum

- $TC:O(n)$
- $SC:O(1)$
- 关于Kadane's算法的一篇不错的文章：https://zhuanlan.zhihu.com/p/85188269

```java
class Solution {
    
    private static int MOD = 1000000007;
    
    public int kConcatenationMaxSum(int[] arr, int k) {
        // 用Kadane's Algo求出单个arr的最大子数组和
        long kadaneSum = getMaxSubArraySumKadane(arr);
        if (k == 1) return (int) kadaneSum % MOD;
        
        // 分别求出单个arr的最大前缀和，最大后缀和以及arr的所有元素的总和
        long prefix = getMaxPrefixSum(arr), suffix = getMaxSuffixSum(arr);
        long totalSum = 0;
        for (int num : arr) totalSum += num;
        
        // 将k个arr相连后得到的最大子数组的和会存在以下几种可能
        // 1. totalSum >= 0 -> suffix + (k - 2) * totalSum + prefix，不能简单地用k * totalSum，因为arr的首尾可能存在负数，去掉这些负数才是真正的最大
        // 2. totalSum < 0 -> suffix + prefix，由于totalSum小于0，多次连接并不能扩大最大子数组和，因此只需要关注头两个连接着的arr的suffix + prefix
        // 3. kadaneSum
        long res = 0;
        // (k - 2) * totalSum在这里可能会溢出，需要先mod
        res = Math.max(res, Math.max(kadaneSum, suffix + (k - 2) * totalSum % MOD + prefix));
        res = Math.max(res, suffix + prefix);
        return (int) res % MOD;
    }
    
    private long getMaxSubArraySumKadane(int[] arr) {
        long maxSum = 0, maxToCurrentEnd = 0;
        for (int i = 0; i < arr.length; i++) {
            maxToCurrentEnd = Math.max(maxToCurrentEnd + arr[i], arr[i]);
            maxSum = Math.max(maxSum, maxToCurrentEnd);
        }
        return maxSum;
    }
    
    private long getMaxPrefixSum(int[] arr) {
        long maxSum = 0, curSum = 0;
        for (int i = 0; i < arr.length; i++) {
            curSum += arr[i];
            maxSum = Math.max(maxSum, curSum);
        }
        return maxSum;
    }
    
    private long getMaxSuffixSum(int[] arr) {
        long maxSum = 0, curSum = 0;
        for (int i = arr.length - 1; i >= 0; i--) {
            curSum += arr[i];
            maxSum = Math.max(maxSum, curSum);
        }
        return maxSum;
    }
}
```