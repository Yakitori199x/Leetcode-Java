# 523. Continuous Subarray Sum (2021.05.05)

https://leetcode.com/problems/continuous-subarray-sum/

## Similar Questions:
- 974. Subarray Sums Divisible by K

## Solution 1: HashMap

- $TC:O(n)$
- $SC:O(n)$
- 因为本题中k的范围是1 <= k <= 2^31 - 1，而n的范围是1 <= nums.length <= 10^5。
- 所以一定要用hashmap而不是array来记录remainder到index的映射，否则会内存溢出。
- 本题的目的就是找出长度大于1的子数组，其对应的数组和为k的倍数，可以转换为找出nums的前缀和是否存在p(j) % k == p(i) % k。
- 为什么呢？因为p(j) % k == p(i) % k时可以得出p(j) = m * k + x，p(i) = n * k + x，m，n和x均为整数。
- 那么p(j) - p(i) = (n - m) * k，正好满足我们的需求。

```java
class Solution {
    public boolean checkSubarraySum(int[] nums, int k) {
        Map<Integer, Integer> preSum = new HashMap<>();
        preSum.put(0, -1);
        int prefix = 0;
        for (int i = 0; i < nums.length; i++) {
            // 因为(a + b) % k = (a % k + b % k) % k
            // 所以我们想得到的是p(i) % k = (p(i-1) + nums[i]) % k = (p(i-1) % k + nums[i] % k) % k, p(i-1) % k即为prefix
            // 也可以简化为prefix = (prefix + nums[i]) % k -> (p(i-1) % k + nums[i]) % k
            // -> (p(i-1) % k % k + nums[i] % k) % k -> (p(i-1) % k + nums[i] % k) % k -> p(i) % k
            prefix = (prefix + nums[i] % k) % k;
            if (preSum.containsKey(prefix)) {
                if (i - preSum.get(prefix) > 1) return true;
            } else {
                preSum.put(prefix, i);
            }
        }
        return false;
    }
}
```