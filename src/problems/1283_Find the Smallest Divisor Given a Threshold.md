# 1283. Find the Smallest Divisor Given a Threshold (2021.03.29)

https://leetcode.com/problems/find-the-smallest-divisor-given-a-threshold/

## Similar Questions:
- 1011. Capacity To Ship Packages Within D Days

## Solution 1: Binary Search

- $TC:O(n*log(max(nums)))$
- $SC:O(1)$

```java
class Solution {
    public int smallestDivisor(int[] nums, int threshold) {
        int n = nums.length;
        if (threshold < n) return -1;
        
        int maxDiv = 0;
        for (int num : nums) maxDiv = Math.max(maxDiv, num);
        int lo = 1, hi = maxDiv + 1;
        while (lo < hi) {
            int mid = (hi - lo) / 2 + lo, cur = 0;
            for (int num : nums) {
                // 一种便利的将除法结果进行ceiling的方法
                cur += (num + mid - 1) / mid;
            }
            
            if (cur > threshold) lo = mid + 1;
            else hi = mid;
        }
        return lo;
    }
}
```